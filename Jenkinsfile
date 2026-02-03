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
        git branch: 'main',
            url: 'https://github.com/<your-org>/customer-onboarding.git'
      }
    }

    stage('Validate Customer') {
      steps {
        sh '''
          echo "Checking if customer already exists..."
          if jq -e ".${PRODUCT}.customers.${CUSTOMER}" customer_onboarding.json > /dev/null; then
            echo "ERROR: Customer already exists for this product!"
            exit 1
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
