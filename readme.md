# 🔥 orchestrator 🔥
![orchestrator](digram.png)

Orchestrator executes all cypress specs across n parallel docker containers based on a configuration file.

## ♟️ Orchestrator features:

* Pares a config file 
* Create n containers machines  in parallel
* Split all specs across all those machines 
* Collect all the execution results from those containers 
* Down all the running containers
* Generate one HTML report that has all specs execution results. 


## 👌 Installation:

```bash
npm -g install 0xislamtaha/orchestrator
```

## 🎮 Usage:

* With the default configuration file i.e, "src/config.json"
```bash
orchestrator
```

* With your configuration file
```bash
orchestrator --config "/path/to/config.json"
```

If you need to **overwrite** any configuration on the fly, simplly path the new configuration as a prameter.
```bash
orchestrator --config ./src/config.json --parallelizm 2 --environment '{"DOCKER_TAG":"master_283"}' --browsers "[chrome, firefox]"
```


## 🔑 Requirements to use orchrestrator:
1- docker-compose file with a cypress service. here is an example of it.

```yml
version: '3'
services:
  SYSTEM_UNDER_TEST:
    container_name: SYSTEM_UNDER_TEST
    image: SYSTEM_UNDER_TEST_IMAGE
    networks:
      modules_cypress_tests_nw: {}

  cypress_service:
    container_name: cypress__container
    image: cypress/browsers:node13.8.0-chrome81-ff75
    depends_on:
      - SYSTEM_UNDER_TEST
    environment:
      - CYPRESS_baseUrl=http://SYSTEM_UNDER_TEST
    volumes:
      - ./cypress/:/cypress
      - ./mochawesome-report:/cypress/report/mochawesome-report
      - /dev/shm:/dev/shm
    networks:
      modules_cypress_tests_nw: {}

networks:
  modules_cypress_tests_nw:
    driver: bridge

```
2- use mochawsome as a reporter in cypress.json, just add the following snippet to your cypress.json.

```json
{
  "reporter": "mochawesome",
  "reporterOptions": {
    "reportDir": "cypress/report/mochawesome-report",
    "overwrite": false,
    "html": false,
    "json": true
  }
}
```

3- Edit the orchestrator [configuration file](/src/config.json) with your configuration. Here is the description of each configuration option.

```
- parallelizm:
    description: number of container machines per browser
    type: Integer
    example: 2

- browser:
    description: list of browsers
    type: list
    example: ["chrome", "firefox"]

- timeout:
    description: timeout of each process of cypress 
    type: string
    example: "20m"

- environment:
    description: enviroment variable to be exported 
    type: dict
    example: {"DOCKER_TAG": "master_283"}

- preCommands: 
    description: list of commands to be executed befor the deployment of the cypress containers
    type: list
    example: ["ls -al", "mkdir -p test"],

- dockerComposePath:
    description: path to the docker compose file.
    type: string
    example: "/opt/code/github/cypress.docker-compose.yml"

- specsHomePath:
    description: path to the specs dir in the host machine.
    type: string
    example: "/opt/code/github/cypress/integration/"

- specsDockerPath:
    description: path to the specs dir in the cypress container.
    type: string
    example: "/cypress/integration"

- cypressContainerName:
    description: the name of cypress service.
    type: sting
    example: "cypress_service"

- mochawesomeJSONPath:
    description: path to the mochawseom dir in the host machine.
    type: string
    example: "mochawesome-report/*.json"

- reportPath:
    description: path to save the generated HTML report dir.
    type: string
    example: "./"

```

## 🎬 To-Do:

* Export it to npm registry.
* Provide --help option.
