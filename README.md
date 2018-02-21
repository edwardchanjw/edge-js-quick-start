# edge-js-quick-start

## Repro steps for https://github.com/agracio/edge-js/issues/24

This issue is electron specific, I have done few modifications to run this app on electron. I know there is separate edge-js project for electron, but I think it only changes the way it is built(against node vs electron) So if we build correctly this project should also run on electron. As we dont have dotnet core on client machines where electron app is installed, we package the standalone app with electron

1. Creating the standalone core app. 
```
cd src\QuickStart.Core
publish.bat
```
published app will be created in \\src\\QuickStart.Core\\bin\\Release\\netcoreapp2.0\\win-x64\\publish


2. Install dependencies
npm install

fixing to build with electron

In `node-modules\edge-js\package.json` remove the install script
```
"scripts": {
    "test": "node tools/test.js"
  },
```
Build the dependencies with electron builder

```
node_modules\.bin\electron-builder install-app-deps
```


3. Run the app
`npm run start`

you will get the expected output as you have dotnet on your path
```
.NET Core welcomes Node.Js
```

4. Now set the path just to include nodejs, removing dotnet from the path.

`set PATH="C:\Program Files\nodejs"`

it will throw following exception

```
App threw an error during load
Error: This is not a published, standalone application and we are unable to locate the .NET Core SDK.  Please make sure that it is installed; see http://microsoft.com/net/core for more details.
    at process.module.(anonymous function) [as dlopen] (ELECTRON_ASAR.js:172:20)
    at Object.Module._extensions..node (module.js:598:18)
    at Object.module.(anonymous function) [as .node] (ELECTRON_ASAR.js:172:20)
    at Module.load (module.js:503:32)
    at tryModuleLoad (module.js:466:12)
    at Function.Module._load (module.js:458:3)
    at Module.require (module.js:513:17)
    at require (internal/module.js:11:18)
    at Object.<anonymous> (D:\r\Repo\edge-js-quick-start\node_modules\edge-js\lib\edge.js:53:8)
    at Object.<anonymous> (D:\r\Repo\edge-js-quick-start\node_modules\edge-js\lib\edge.js:181:3)
Terminate batch job (Y/N)? Y
```

5. If we comment out the exeption throwing section and build the edge-js, app works as expected
```
    host_mode_t mode = coreclr_exists_in_dir(edgeAppDir) ? host_mode_t::standalone : host_mode_t::muxer;

	// if (mode == host_mode_t::standalone && dotnetExecutablePath.empty())
	// {
	// 	throwV8Exception("This is not a published, standalone application and we are unable to locate the .NET Core SDK.  Please make sure that it is installed; see http://microsoft.com/net/core for more details.");
	// }

	pal::string_t configFile, devConfigFile, sdkPath;
```
`node_modules\.bin\electron-builder install-app-deps`

This is also reproducible on mac-os following similar steps.

**electron version is  1.8.2**