# prototype
Project created as a prototype for cloud applications. Project is based on React for frontend, Spring Cloud for backend services and React Native as platform for Mobile application. Every part has own readme, where are described details and uses.

### be_cloud
Spring Cloud application as a backend. Is created as Maven project with modules, which represents microservices of the project.<br>
All layers are created or built to the Docker containers and are prepared in the Docker composers.<br>
For more details see readme inside this directory.

### fe_web
Frontend layer based on React with react router and material design. Provides a complete UI for prototype functionalities.<br>
This layer is also prepared into the Docker container and as a part of the project's Docker composer.<br>
For more details see readme inside this directory.

### docker-composer
All Docker composers necessary for a fully manageable microservices of the prototype project.<br>
Has two parts:
1. management layer, where are all cloud, data management (databases, message brokers, logging harvesters, etc.) and security services
2. project layer, where are all functional services, api gateway, service load balancer and React UI

---

## Prepare environment

Before starting a project is good to prepare an environment and developing tools.
Is preferred to use Intellij as developing platform, which covers React and Spring Boot code review and other abilities, so also this setup descriptions is focused to Intellij.

### Install Docker with Portainer

Docker is a base here platform for containers, and you can use it together with Portainer, what is a UI manager for containers.

#### Docker installation

If you are using Windows OS, here is a link to download Docker desktop https://docs.docker.com/desktop/install/windows-install/ <br>
Just install it by described steps, and you are ready to use it.

#### Portainer installation

Portainer is a Docker container, which provides for you a UI manager on set port, run as standalone application.<br>
Before Portainer installation you need to create a volume for Portainer data:

<code>docker volume create portainer_data</code>

After that you can install a Portainer container (community edition):

<code>docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest</code>

After installation check, if everything is up and running <code>docker ps</code>

If yes, you can open link https://localhost:9443

The rest of the setup is described here: https://docs.portainer.io/start/install/server/setup


### Install Node.js

For Node.js version is preferred 9 and higher.

1. Download Node.js (it depends on you, which package you will use - Windows, MacOS or Linux) https://nodejs.org/en/download/
2. Install node.js after unpacking downloaded package (for Windows after installing  a msi file) - check system paths if nodejs or npm is added correctly
3. use command <code>npm install</code>

### Setup Intellij

If you are using another developing tool (Visual Studio, Eclipse, etc.) steps are similar.

At first, you need to download the project from GitHub:

Menu: <code>File > New > Get project from Version Control...</code>

Here select a GitHub options, connect to your GitHub account and download proto-proj project. After that you have to prepare a Java version and Run services for all included modules.

Menu: <code>File > Project Structure > </code>

tab: **Project**:
* **SDK**: add version 19
* **Language level**: 17

tab **Modules**:
* **Language level**: 17 for all Spring Boot services (admin-service, user-service)

Menu: <code>Edit Configurations... > add > npm > </code>

* **Name**: react frontend
* **package.json**: <code><path_to_project>\proto-proj\fe_web\package.json</code>
* **Command**: start

<code> > add > Spring Boot > </code>

* **Name**: admin-service
* **module SDK**: 19
* **module**: admin-service
* **main Class**: com.pwc.AdminServiceApplication
  
  
  <code> > add > Spring Boot > </code>

* **Name**: user-service
* **module SDK**: 19
* **module**: user-service
* **main Class**: com.pwc.UserServiceApplication

Intellij notify you if you can create a Services tool for multiple Spring Boot applications:

> IntelliJ's notification: Manage multiple Spring Boot run configurations in the Services tool window

Click on **Use Services**

Now you have prepared developing tools to use.

### Next steps

Next step is to prepare management layer for backend services. All these services are already prepared in the Docker containers and composed together by Docker composer (see `./docker-composer/README.md`).

### How to get a token from KeyCloak

If you need to get a token from a KeyCloak, you have to send a POST request like this:

> curl -L -X POST \
>   'http://localhost:8181/realms/ProtoProj/protocol/openid-connect/token' \
>   -H 'Content-Type: application/x-www-form-urlencoded' \
>   --data-urlencode 'client_id=login-app' \
>   --data-urlencode 'client_secret=<add own cliend secret>' \
>   --data-urlencode 'grant_type=password' \
>   --data-urlencode 'scope=email roles profile' \
>   --data-urlencode 'username=<add own user name>' \
>   --data-urlencode 'password=<add own user password>'

From response get value `access_token`, what represents your token for communication with API gateway in case of testing.

> curl --location --request GET 'http://localhost:8190/sample' \
> --header 'Accept: application/json' \
> --header 'Authorization: Bearer <add requested access token>'
