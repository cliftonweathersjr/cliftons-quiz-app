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

Once selecting `yes` for the conifiguration. Select `AWS Profile` for the authentication method and `amplify-dev-lcoal` for the profile. Lastly, select `no` for the sharing non-sensitive configutarion information.

## Step Three: Setting Up Cognito Authentication

Run the command `amplify add auth`. After running the command, answer the following questions with the asnwers below.
```
Do you want to use the default authentication and security configuration? Default Configuration
How do you want users to be able to sign in? Email
Do you want to configure advanced settings? No, I am done.
```
After this is complete, run the command `amplify push` and `yes` to continue. These commands will push everything out to AWS for the backend environment. The next thing that needs to happen is to give the Cognito, an UI to enforce authentication. Accomplish this with the following steps.

In the src directory of the freshly created application, navigate to `App.js`. Once viewing, replace the code with the following:
```
import React from 'react';
import './App.css';

// Imports the Amplify library from 'aws-amplify' package. This is used to configure your app to interact with AWS services.
import {Amplify} from 'aws-amplify';

// Imports the Authenticator and withAuthenticator components from '@aws-amplify/ui-react'.
// Authenticator is a React component that provides a ready-to-use sign-in and sign-up UI.
// withAuthenticator is a higher-order component that wraps your app component to enforce authentication.
import { Authenticator, withAuthenticator } from '@aws-amplify/ui-react';

// Imports the default styles for the Amplify UI components. This line ensures that the authenticator looks nice out of the box.
import '@aws-amplify/ui-react/styles.css';

// Imports the awsExports configuration file generated by the Amplify CLI. This file contains the AWS service configurations (like Cognito, AppSync, etc.) specific to your project.
import awsExports from './aws-exports';

// Imports the Quiz component from Quiz.js for use in this file.
// import Quiz from './Quiz';

// Configures the Amplify library with the settings from aws-exports.js, which includes all the AWS service configurations for this project.
Amplify.configure(awsExports);

function App() {
  return (
    <div className="App">
      <Authenticator>
        {({ signOut }) => (
          <main>
            <header className='App-header'>
              {/* Quiz Component */}
              {/*<Quiz />*/}
              {/* Sign Out Button */}
              <button 
                onClick={signOut} 
                style={{ 
                  margin: '20px', 
                  fontSize: '0.8rem', 
                  padding: '5px 10px', 
                  marginTop: '20px'
                }}
              >
                Sign Out
              </button>
            </header>
          </main>
        )}
      </Authenticator>
    </div>
  );
}

export default withAuthenticator(App);
```
After that is complete, run the command `npm install aws-amplify @aws-amplify/ui-react` in your IDE terminal. This will install the Amplify libraries that give the login interface. Once this is complete, the application can now be run using the command `npm start`. The website can be accessed using `localhost:3000`. From there, you can create an account and test the functionality of the Cognito service by the checking the user pool.

## Step Four: Adding Functionality For Quiz

To give the site something to access once past the authentication, functionality will need to be added for the quiz. To do this, we need to add two files. The first is going to be `Quiz.js`, create this file in the `src` directory and add the following code:
```
import React, { useState } from 'react';
import quizData from './quizData';

function Quiz() {
  const [currentQuestion, setCurrentQuestion] = useState(0);
  const [score, setScore] = useState(0);
  const [showScore, setShowScore] = useState(false);
  const [selectedAnswer, setSelectedAnswer] = useState(""); 
  const [isCorrect, setIsCorrect] = useState(null);

  const handleAnswerOptionClick = (option) => {
    const correctAnswer = quizData[currentQuestion].answer;
    setSelectedAnswer(option);
    if (option === correctAnswer) {
      setScore(score + 1);
      setIsCorrect(true);
    } else {
      setIsCorrect(false);
    }

    // Delay moving to the next question to allow the user to see feedback
    setTimeout(() => {
      const nextQuestion = currentQuestion + 1;
      if (nextQuestion < quizData.length) {
        setCurrentQuestion(nextQuestion);
        setIsCorrect(null); // Reset for the next question
        setSelectedAnswer(""); // Reset selected answer
      } else {
        setShowScore(true);
      }
    }, 1000); // Adjust time as needed
  };

  return (
    <div className='quiz'>
      {showScore ? (
        <div className='score-section'>
          You scored {score} out of {quizData.length}
        </div>
      ) : (
        <>
          <div className='question-section'>
            <div className='question-count'>
              <span>Question {currentQuestion + 1}</span>/{quizData.length}
            </div>
            <div className='question-text'>{quizData[currentQuestion].question}</div>
          </div>
          <div className='answer-section'>
            {quizData[currentQuestion].options.map((option) => (
              <button 
                onClick={() => handleAnswerOptionClick(option)} 
                key={option}
                style={{ backgroundColor: selectedAnswer === option ? (isCorrect ? 'lightgreen' : 'pink') : '' }}
              >
                {option}
              </button>
            ))}
          </div>
          {selectedAnswer && (
            <div style={{ marginTop: '10px' }}>
              {isCorrect ? 'Correct! 🎉' : 'Sorry, that’s not right. 😢'}
            </div>
          )}
        </>
      )}
    </div>
  );
}

export default Quiz;
```
The second file need will be `quizData.js`. This will provide the questions and answerse for the quiz. For this file, once created, add the following code:
```
const quizData = [
    {
      question: "What was the first video game ever made?",
      options: ["Pong", "Spacewar!", "Tetris", "Computer Space"],
      answer: "Spacewar!"
    },
    {
      question: "Which company developed the first commercial antivirus software?",
      options: ["Symantec", "McAfee", "Norton", "Kaspersky Lab"],
      answer: "McAfee"
    },
    {
      question: "Which animal is featured in the official PHP logo?",
      options: ["Elephant", "Hippo", "Giraffe", "Lion"],
      answer: "Elephant"
    },
    {
      question: "What does 'HTTP' stand for?",
      options: ["HyperText Transfer Protocol", "Hyperlink Transfer Technology Protocol", "Hyperlink Text Transfer Protocol", "HyperText Technology Protocol"],
      answer: "HyperText Transfer Protocol"
    },
    {
      question: "Which programming language is known as the backbone of the World Wide Web?",
      options: ["Java", "C#", "Python", "HTML"],
      answer: "HTML"
    },
    {
      question: "What is the name of the world's first computer programmer?",
      options: ["Charles Babbage", "Ada Lovelace", "Alan Turing", "Grace Hopper"],
      answer: "Ada Lovelace"
    },
    {
      question: "In what year was the iPhone first introduced?",
      options: ["2005", "2007", "2009", "2011"],
      answer: "2007"
    },
    {
      question: "What was Google's original name?",
      options: ["BackRub", "Googol", "SearchMaster", "WebSearch"],
      answer: "BackRub"
    },
    {
      question: "Which of these companies was not founded in a garage?",
      options: ["Amazon", "Google", "Apple", "Microsoft"],
      answer: "Amazon"
    },
    {
      question: "What does 'GPU' stand for?",
      options: ["Graphical Processing Unit", "Graphics Performance Unit", "Graphics Processing Unit", "Graphical Performance Unit"],
      answer: "Graphics Processing Unit"
    },  
    {
      question: "What is the capital of France?",
      options: ["New York", "London", "Paris", "Dublin"],
      answer: "Paris"
    },
    {
      question: "Who painted the Mona Lisa?",
      options: ["Vincent Van Gogh", "Leonardo da Vinci", "Pablo Picasso", "Claude Monet"],
      answer: "Leonardo da Vinci"
    },
    {
      question: "What is the largest planet in our solar system?",
      options: ["Earth", "Jupiter", "Saturn", "Mars"],
      answer: "Jupiter"
    }
  ];
  
  export default quizData;
  ```
That will it for new files. However, there is commented-out code in the `App.js` file that will need to be revised. Return that file and ensure the following lines read as shown:
```
Line 19: import Quiz from './Quiz';
Line 32: <Quiz />
```
Once that is completed, save all of the files and there should now be a working quiz behind the site authentication.

## Step Five: Setting Up CI/CD with GitHub

To create this CI/CD, a new respository is going to be needed. Once that new repository is created and named, return to the IDE to push the local files/code to the repository. To do this following the list of commands:
```
git init
git add .
git commit -m "Initial Commit"
git branch -M main
git remote add origin <repository URL>
git push -u origin main
```
The files should now reside in the new repository that was just made.

Next step will be to connect the repsository to the front end with AWS Amplify. Navigate to Amplify on your AWS console and select the quiz app. Select the action to host/deploy an app, it will then provide different options to host with. For this project, the GitHub option is what will be used. once selected, click `Connect branch`. It will prompt you to select the repository. The repository should be listed, but if not, select `View GitHub permissions` and give Amplify access to the necessary repository. Once done, select the right repository and ensure the branch is `main`. Most of the build settings should populate. Ensure the environment is `dev` and check the box next to `Enable full-stack continuous deployments (CI/CD)`. Next, a service role will need to be created to work properly. Select the option to create a role and specify Amplify as the service. For the permissions policies, add the `AdminsitratorAccess-Amplify` polciy to ensure no errors occur when deploying, then click next and create role.

Once the role is created, get back to the Amplify console where the app is being built. After selecting the newly created service role, click next and review the information for any errors. If everything is right, select `save and deploy`. You should then be redirected to a page to watch the build process of the application.

The purpose of this set up using AWS Amplify and Github is to create a pipeline that allows for the deployment of changes in the file to be automated. To test this, you can edit the quizData.js file (remove a question) residing in your GitHub repository and commit the changes. Once the commit has been pushed, you can check the Amplify consoleand notice that it has already begun the redploy to include the new changes. After finsihing, the changes should be present with a refresh.


