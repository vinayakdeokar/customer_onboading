pipeline {
  agent any

  parameters {
    choice(
      name: 'PRODUCT',
      choices: ['edps', 'm360', 'r360', 'mp360'],
      description: 'Product name'
    )

    string(
      name: 'CUSTOMER',
      description: 'Customer ID (example: abc01)'
    )
  }

  stages {

    stage('Checkout Repo') {
      steps {
        git url: 'https://github.com/vinayakdeokar/customer_onboading.git', branch: 'main'
      }
    }

    stage('Validate Customer') {
      steps {
        sh '''
          echo "Checking if customer already exists..."

          if jq -e ".customers.${CUSTOMER}.products.${PRODUCT}" customer_onboarding.json > /dev/null; then
            echo "ERROR: Customer already exists for this product!"
            exit 1
          fi

          echo "Customer not found. Proceeding to add."
        '''
      }
    }

    stage('Add Customer') {
      steps {
        sh '''
          echo "Adding customer to JSON..."

          jq --arg customer "$CUSTOMER" --arg product "$PRODUCT" '
          .customers[$customer].products[$product].groups = {
            ("adg-" + $product + "-databricks-contributor-qa"): {
              "permissions": ["USE_CATALOG", "USE_SCHEMA", "SELECT"]
            }
          }
          ' customer_onboarding.json > tmp.json

          mv tmp.json customer_onboarding.json
        '''
      }
    }

    stage('Commit & Push') {
      steps {
        sh '''
          git config user.email "jenkins@local"
          git config user.name "jenkins-bot"

          git add customer_onboarding.json
          git commit -m "Onboard customer ${CUSTOMER} for ${PRODUCT}"
          git push origin main
        '''
      }
    }
  }
}
