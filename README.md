# Create a Quiz using AWS Amplify and Cognito (with CI/CD)

In this porject, I will be building a React app (quiz app) using AWS Amplify and Cognito. I will also be setting up continuous integration and continuous deployment (CI/CD) using GitHub.

## Prerequisites

+ IDE
+ Node (Can be installed at Nodejs.org

  
## Step One: Configuring Amplify CLI
To start off this step, create a directory for the application and change into that folder. From there, run the command `npm install -g @aws-amplify/cli`. This will install the Amplify CLI. Afterwards, run `amplify configure`. This will return a pop-up for you to sign into the AWS console. While in the console, an IAM user will need to be created for the Amplify CLI. To do such, navigate to the IAM's User section in the console. Ocne there, create a user with the name `amplify-dev`. When setting permissions, insteading of adding the user to a group, attach policies directly. Under the permissions policies, search for the policy name `AdministratorAccess-Amplify`. Once selected, click next and then create user. After creating the user, an Access Key will be needed for the IDE to use when running commands. To create this, navigate to the security credentials section for the newly created user. The Access Key section will have an option to create a access key, select it. The use for this key will be Command Line Interface because it will be used in the terminal of the IDE. Once that is selected and the confirmatio at the bottom is checked, click next and then create access key. Take note of the access key information because the credentials will be needed by the terminal. The IDE provides a link with detailed instructions, if needed.

After creating the user and it's access keys, go back to the terminal and hit enter. The IDE will ask for the current region and after hitting two more times, a request for the Access Key information will be given. Enter the information as it is requested. The last part to this step will be giving the profile a name, the name should be `amplify-dev-local`.

## Step Two: Create React App and Initialize the Amplify Project

To create the React app, run the command `npx create-react-app <name of your app>`. This will create a folder inside of the current directory with the name of the app. Once the command is finsihed, change directory to that folder to begin initializing the Amplify project.

To initialize the Amplify project, run the command `amplify init`. Once this command runs, the name of the application will need to be specified. This name will be the name of the application on the Amplify console and is not connect to the name of the application on the local machine. After entering the name, the Project information will presented and the terminal will ask if the information is correct. Ensure the information matches the information below before answering, `yes`.
```
Project information
Name: <name of your app>
Environment: dev
Default editor: <current IDE>
App type: javascript
Javascript framework: react
Source Directory Path: src
Distribution Directory Path: build
Build Command: npm.cmd run-script build
Start Command: npm.cmd run-script start
```





