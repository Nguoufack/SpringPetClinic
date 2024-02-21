CODE_CHANGES = getGitChanges()
def gv
pipeline{
  agent main
  environment{
    NEW_VERSION = '1.3.0'
    SERVER_CREDENTIALS = credentials('ID of the jenkins environment')
  }

  tools{
    # the tools attribute provides the build tools for the project.
    # for example maven for build the code
    # make sure to go to jekins->Global Tool Configuration->select the tool and set the tool name
    # example with maven tool named as Maven can be seen below
      maven 'Maven'
  }

  parameters{
    # for external config which should be included in build like version of app to be deploied
      # example of params
      string(name: 'VERSION', defaultValue: '', description: 'version to deploy on production')
      choice(name: 'VERSION', choices: ['1.1.0', '1.2.0'], description: '')
      booleanParam(name: 'executeTests', defaultValue: true, description: '')
  }
  
  stages{
    stage ("init"){
      step{
          # jenkinsfile support groovy script by adding Script blocks
          script{
            # scripting here can be var dec, func defin ..
            # loading other groovy file, example
            gv = load "script.groovy"
            # nb: define the variable out of the pipeline block
          }
        }   
      }
    stage("build"){
      steps {
          echo 'building the application'
          echo "building version ${NEW_VERSION}"
          # calling the script function from the groovy file
          # nd: the params and vars declared & defined in the jenkinsfile are accessible in groovy file
          gv.buildApp()
      }
    }

    stage("test"){
      when {
        expression{
          params.excuteTests
          # calling the script function for testing define in groovy file
          gv.testApp()
        }
        
        expression{
          env.BRANCH_NAME == 'main' && CODE_CHANGES == true
        }
      }
      steps{
          echo 'testing the application'
          echo "deploying with ${SERVER_CREDENTIALS}"
          # the above line could be substituted with the one below.
          # the declared environment variable must not be declared as done above
          withCredentials([usernamePasword(credentials: 'ID of the jenkins environment', 
                            usernameVariable: USER, passwordVariable: PWD)]){
            # object call should be done the square brackets
            sh "some script ${USER} ${PWD}"
            # sh denote a shell script command
          }
      }
    }

    stage("deploy"){
      steps{
          echo 'deploying the application'
          echo "deploying version ${params.VERSION}"
          gv.deployApp()
      }
    }
  }

  post{
    always{   
    }
    failure{
    }
  }
}
