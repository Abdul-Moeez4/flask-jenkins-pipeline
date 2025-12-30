pipeline {
  agent any

  options {
    timestamps()
  }

  environment {
    VENV_DIR = ".venv"
    DEPLOY_DIR = "/tmp/flask-deploy"
  }

  stages {

    stage('Clone Repository') {
      steps {
        echo "Repo cloned"
        sh 'ls -lah'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh '''
          set -e
          python3 --version
          python3 -m venv "${VENV_DIR}"
          . "${VENV_DIR}/bin/activate"
          pip install --upgrade pip
          pip install -r requirements.txt  
        '''
        //req.txt, where our dependicies are stored
      }
    }

    stage('Run Unit Tests') {
      steps {
        sh '''
          set -e
          . "${VENV_DIR}/bin/activate"
          pytest -q
        '''
      }
    }

    stage('Build Application') {
      steps {
        sh '''
          set -e
          rm -rf dist || true
          mkdir -p dist
          tar -czf dist/flask_app.tar.gz \
            --exclude="${VENV_DIR}" \
            --exclude=".git" \
            --exclude="dist" \
            .
          ls -lah dist/
        '''
      }
    }

    stage('Deploy') {
      steps {
        sh '''
          set -e
          mkdir -p "${DEPLOY_DIR}"
          cp -f dist/flask_app.tar.gz "${DEPLOY_DIR}/"
          echo "Simulated deploy complete -> ${DEPLOY_DIR}"
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
