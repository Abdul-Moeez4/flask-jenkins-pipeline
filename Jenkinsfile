pipeline {
  agent any

  options { timestamps() }

  environment {
    VENV_DIR = ".venv"
    DEPLOY_DIR = "C:\\temp\\flask-deploy"
  }

  triggers {
    githubPush()
  }

  stages {

    stage('Clone Repository') {
      steps {
        echo "Repo cloned by Jenkins SCM checkout"
        bat 'dir'
      }
    }

    stage('Install Dependencies') {
      steps {
        bat '''
          python --version
          python -m venv %VENV_DIR%
          call %VENV_DIR%\\Scripts\\activate
          python -m pip install --upgrade pip
          pip install -r requirements.txt
        '''
      }
    }

    stage('Run Unit Tests') {
      steps {
        bat '''
          call %VENV_DIR%\\Scripts\\activate
          pytest -q
        '''
      }
    }

    stage('Build Application') {
      steps {
        bat '''
          if exist dist rmdir /s /q dist
          mkdir dist

          rem simple "build": copy files into dist
          copy app.py dist\\app.py

          if exist tests xcopy /E /I /Y tests dist\\tests
          copy requirements.txt dist\\requirements.txt
          dir dist
        '''
      }
    }

    stage('Deploy (Simulated)') {
      steps {
        bat '''
          if not exist "%DEPLOY_DIR%" mkdir "%DEPLOY_DIR%"
          xcopy /E /I /Y dist "%DEPLOY_DIR%\\dist"
          echo Simulated deploy complete -> %DEPLOY_DIR%
        '''
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'dist/**', fingerprint: true
    }
  }
}
