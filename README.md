# Framework prototype for [Amazon](https://www.amazon.com/) automation testing.
This project provides automated test example framework for [Amazon](https://www.amazon.com/)
to find and order the following items:
1. Latest Playstation with a most profitable price for your husband.
2.TV show with a highest review rate for your Mom.
3.Star Wars Lego for your 9 years old kid.
4.Montale Wild Pears Parfum (50 milliliters) for your girlfriend.
5.Red NIKE running shoes (42 EU size) for your boyfriend.

## 1. Configuration details:
The project uses **TestNG**  testing framework and **Allure** test report framework to create test execution reports.
**Gradle** configure project build automation.
To supply test data in a more flexible way use TestNG DataProvider wrapper - [Test Data Supplier](https://github.com/sskorol/test-data-supplier).
For browser management and taking screenshots in project was used [WebDriver Supplier](https://github.com/sskorol/webdriver-supplier).
All scenarios tested on  Firefox 57.0  and Chrome 62.0 browsers in **Docker** containers via **Selenoid**:
###1.1. Create config/browsers.json configuration file with content:

```
{
  "firefox": {
    "default": "57.0",
    "versions": {
      "57.0": {
        "image": "selenoid/vnc:firefox_57.0",
        "port": "4444"
      }
    }
  },
  "chrome": {
    "default": "62.0",
    "versions": {
      "62.0": {
        "image": "selenoid/vnc:chrome_62.0",
        "port": "4444"
      }
    }
  }
}
```
###1.2.  To run tests by multi-container Docker use a Docker Compose YAML file to configure your application?s services
**for Windows**:
```
version: '3'
services:
  selenoid:
    image: "aerokube/selenoid:1.4.0"
    network_mode: bridge
    ports:
      - "4444:4444"
    volumes:
      - "/C/your directory:/etc/selenoid" # assumed current dir contains browsers.json located on C: drive
      - "/C/your directory for video files:/output" # assumed current dir contains video files located on C: drive
      - "//var/run/docker.sock:/var/run/docker.sock"
    environment:
      - "OVERRIDE_VIDEO_OUTPUT_DIR=/C/your directory for video files /"
    command: ["-limit", "6", "-conf", "/etc/selenoid/browsers.json", "-video-output-dir",
 "/your directory for video files located on C: drive"]
  selenoid-ui:
    image: "aerokube/selenoid-ui"
    network_mode: bridge
    links:
      - selenoid
    ports:
      - "8081:8080"
    command: ["--selenoid-uri", "http://selenoid:4444"]
```

**for Mac and Linux** use next docker compose YAML file configuration:
```
version: '3'
services:
  selenoid:
    image: "aerokube/selenoid:1.4.0"
    network_mode: bridge
    ports:
      - "4444:4444"
    volumes:
    - "$PWD:/etc/selenoid/"
    - "$PWD:/output"
    - "//var/run/docker.sock:/var/run/docker.sock"
    environment:
    - "OVERRIDE_VIDEO_OUTPUT_DIR=$PWD"
    command:  ["-limit", "6", "-conf", "/etc/selenoid/browsers.json", "-video-output-dir", "/output"]
    selenoid-ui:
    image: "aerokube/selenoid-ui"
    network_mode: bridge
    links:
    - selenoid
    ports:
    - "8081:8080"
    command: ["--selenoid-uri", "http://selenoid:4444"]
```
## 2.  Run Configuration details:
To create Run/Debug Configuration for Gradle use `clean test` Gradle tasks. For Allure report generation use
`allureReport` and then `allureServe` Gradle tasks.
