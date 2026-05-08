pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    environment {
        BUILD_BRANCH_PATTERN = '.*'
    }

    stages {
        stage('Branch Info') {
            steps {
                script {
                    def branchName = env.CHANGE_BRANCH ?: env.BRANCH_NAME ?: env.GIT_BRANCH ?: 'branch selected by Jenkins'
                    branchName = branchName.replaceFirst(/^origin\//, '')

                    if (!(branchName ==~ env.BUILD_BRANCH_PATTERN)) {
                        error "Branch ${branchName} is not enabled by BUILD_BRANCH_PATTERN=${env.BUILD_BRANCH_PATTERN}"
                    }

                    env.ACTIVE_BRANCH_NAME = branchName
                    echo "Allowed branch pattern: ${env.BUILD_BRANCH_PATTERN}"
                    echo "Running branch: ${env.ACTIVE_BRANCH_NAME}"
                }
            }
        }

        stage('Checkout Code') {
            steps {
                echo 'Checking out the branch selected by Jenkins'
                checkout scm
            }
        }

        stage('Setup Python') {
            steps {
                bat '''
                py -m venv venv
                call venv\\Scripts\\activate
                python -m pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Automation Suite') {
            steps {
                bat 'call venv\\Scripts\\activate && pytest -v -s tests\\test_bi_mat_cleopatra_flow.py'
                bat 'call venv\\Scripts\\activate && pytest -v -s tests\\test_cung_hy_phat_tai_flow.py'
                bat 'call venv\\Scripts\\activate && pytest -v -s tests\\test_hac_thoai_ngo_khong_flow.py'
                bat 'call venv\\Scripts\\activate && pytest -v -s tests\\test_kho_bau_tu_linh_flow.py'
                bat 'call venv\\Scripts\\activate && pytest -v -s tests\\test_linh_chau_at_ty_flow.py'
                bat 'call venv\\Scripts\\activate && pytest -v -s tests\\test_mini_poker_flow.py'
                bat 'call venv\\Scripts\\activate && pytest -v -s tests\\test_sac_xuan_cho_tet_flow.py'
                bat 'call venv\\Scripts\\activate && pytest -v -s tests\\test_son_tinh_thuy_tinh_flow.py'
                bat 'call venv\\Scripts\\activate && pytest -v -s tests\\test_taixiu_mini_flow.py'
                bat 'call venv\\Scripts\\activate && pytest -v -s tests\\test_tren_duoi_mini_flow.py'
                bat 'call venv\\Scripts\\activate && pytest -v -s tests\\test_txmd5_mini_flow.py'
            }
        }
    }

    post {
        always {
            echo "Finished branch: ${env.ACTIVE_BRANCH_NAME ?: env.BRANCH_NAME ?: env.GIT_BRANCH ?: 'branch selected by Jenkins'}"
            archiveArtifacts artifacts: 'reports/**/*,logs/**/*', allowEmptyArchive: true
        }

        success {
            echo 'Pipeline completed successfully'
        }

        failure {
            echo 'Pipeline failed'
        }
    }
}
