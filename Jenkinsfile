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
      description: 'Customer ID (example: xxcc01)'
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
          echo "Checking if customer already exists for product..."

          if jq -e ".customers.${CUSTOMER}.products.${PRODUCT}" customer_onboarding.json > /dev/null; then
            echo "ERROR: Customer already exists for this product!"
            exit 1
          else
            echo "Customer not found. Safe to onboard."
          fi
        '''
      }
    }

    stage('Add Customer (Manual Step)') {
      steps {
        echo """
✔ Validation passed

👉 Please add customer '${CUSTOMER}' under product '${PRODUCT}'
👉 Update customer_onboarding.json
👉 Commit & push the change

This pipeline will stop here.
"""
      }
    }
  }
}
