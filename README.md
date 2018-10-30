# App-AutoScaler-UI

The UI project for [App-AutoScaler](https://github.com/cloudfoundry-incubator/app-autoscaler). Written with Golang/ReactJS/Redux/ES6/Webpack stack, tested by ginkgo/jest/enzyme.
* **ServiceView page**: bound apps info
* **AppView Page**: 
  * **Policy Page**: view/edit application policy
  * **Metrics Page**: metrics dashboard
  * **History Page**: scaling history
  
## Cloudfoundry Client Scope
### bosh lite
```
openid,cloud_controller.read,cloud_controller.write,cloud_controller.admin
```
  
## Run Locally -- Getting Started

### Setup
1. Install go & node & npm
  - [go](https://golang.org/dl/)
  - [node](https://nodejs.org)
  - [npm](https://www.npmjs.com)
  
2. Download project
```
$ git clone https://github.com/cloudfoundry-incubator/app-autoscaler-ui.git
```

3. Install node dependencies
```
$ cd app-autoscaler-ui/scalerui/view
$ npm install
$ npm run build
```

4. Update front-end folder path to yours in `app-autoscaler-ui/scalerui/src/scalerui/exampleconfig/exampleconfig.yml` 
```
  ...
  view_path: "/Users/zyjiaobj/app-autoscaler-ui/scalerui-release/src/scalerui/view/dev/"
  ...
```

5. Go parts setup
```
$ cd app-autoscaler-ui/scalerui
$ git pull && git submodule foreach --recursive git submodule sync && git submodule update --init --recursive
$ source .envrc
$ go build ./src/scalerui/ui/cmd/ui/main.go
$ chmod +x main
```

### Start
```
$ ./main -c ./src/scalerui/exampleconfig/exampleconfig.yml
```
* ServiceView page: https://localhost:8080/manage/{SERVICE_INSTANCE_ID}
* AppView Page: https://localhost:8080/apps/{APPLICATION_ID}

### Test
  - `npm run test`

## Bosh Lite Deployment

1. Install [Bosh-cli-v2](https://bosh.io/docs/cli-v2.html#install)

2. Install and start [BOSH-Deployment](https://github.com/cloudfoundry/bosh-deployment), following its [README](https://github.com/cloudfoundry/bosh-deployment/blob/master/README.md).

3. Install [CF-deployment](https://github.com/cloudfoundry/cf-deployment/blob/master/cf-deployment.yml)

4. Install [app-autoscaler](https://github.com/cloudfoundry-incubator/app-autoscaler-release/blob/develop/templates/app-autoscaler-deployment.yml), following its [README](https://github.com/cloudfoundry-incubator/app-autoscaler-release/blob/develop/README.md)

5. Create and upload app-autoscaler-ui release 

```
$ git clone https://github.com/cloudfoundry-incubator/app-autoscaler-ui.git
$ cd scalerui/scalerui-release
$ bosh create-release
$ bosh -e YOUR_ENV upload-release
```

6. Deploy app-autoscaler-ui

```
$ bosh -e YOUR_ENV -d scalerui \
     deploy -n templates/scalerui-deployment.yml \
     -v system_domain=bosh-lite.com \
     -v scalerui_host=scalerui \
     -v cf_client_scope=openid,cloud_controller.read,cloud_controller.write,cloud_controller.admin \
     -v console_urls=https://console.bosh-lite.com
```

7. Access:
* ServiceView page: https://scalingconsole.bosh-lite.com/manage/{SERVICE_INSTANCE_ID}
* AppView Page: https://scalingconsole.bosh-lite.com/apps/{APPLICATION_ID}

## Deploy UI as a CF applictaion

1. Make sure app-autoscaler is available in your CF environment
2. Build and push UI as a CF applictaion
```
$ git clone https://github.com/cloudfoundry-incubator/app-autoscaler-ui.git
$ cd app-autoscaler-ui/scalerui/scripts
$ ./build.sh -curls <CONSOLE_URLS> -cd <CF_DOMAIN> -cu <CF_USER> -cp <CF_PASSWORD> -co <CF_ORG> -cs <CF_SPACE> -host <UI_APP_NAME> -ac <CLIENT_ID> -as <CLIENT_SECRET> -aso <CLIENT_SCOPE>
```
3. Access:
* ServiceView page: https://<UI_APP_NAME>.<CF_DOMAIN>/manage/{SERVICE_INSTANCE_ID}
* AppView Page: https://<UI_APP_NAME>.<CF_DOMAIN>/apps/{APPLICATION_ID}


## Project Structures

### `view/`: typical react/webpack coding styles. Here is instructions for important folders and files

| Folder            | Description          |
|:------------------|:---------------------|
| __mocks__/        | mocked componets for jest |
| __test__/         | jest testing codes, corresponds to src folder |
| coverage/         | testing report, generated by `npm run test:coverage` |
| dev/              | build files, generated by `npm run build` |
| lib/              | imported custom libraries |
| node_modules/     | dependencies, generated by `npm install` |
| src/              | react source codes |
| src/actions/      | flux actions |
| src/common/       | common tools/functions |
| src/components/   | react views |
| src/constants/    | constants for project |
| src/images/       | image resources |
| src/reducers/     | redux reducers for actions |
| src/sources/      | api calls for backend |
| src/stores/       | flux data stores |
| src/styles/       | sass/css style sheets |
| src/app.js        | entrance for project source |
| src/config.js     | config file |
| src/index.html    | entrance for project source |
| src/router.js     | route configuration |
| package.json      | main config for project |
| init.js           | initial actions before build |
| init.test.js      | initial actions before build |
| webpack.config.js | webpack lifecycle config for dev |
| webpack.pro.config.js | webpack lifecycle config for build |
