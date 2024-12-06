pipeline {
    agent any  

    environment {
        // Defina variáveis de ambiente, como sua API Key para DefectDojo, se necessário
        DEFECTDOJO_API_KEY = '21ec9553ffa670bc4e034073e5b39ca60084bf78'
        DEFECTDOJO_URL = 'http://localhost:8080/api/v2/import-scan/'
        DEFECTDOJO_TOKEN = '21ec9553ffa670bc4e034073e5b39ca60084bf78'
    }

    stages {
        stage('Checkout') {
            steps {
                // Passo para obter o código do repositório
                git url: 'https://github.com/erev0s/VAmPI.git', branch: 'master'

            }
        }

        stage('Run Semgrep') {
            steps {
                // Executa o Semgrep e gera o relatório semgrep_report.json
                sh 'semgrep --config auto --json --output semgrep_report.json .'
                sh 'ls -l semgrep_report.json'  // Verifica se o relatório foi gerado corretamente
            }
        }

        stage('Run Trivy') {
            steps {
                // Executa o Trivy e gera o relatório trivy_report.json
                sh 'trivy fs --scanners vuln --format json --output trivy_report.json .'
            }
        }

        stage('Run Gitleaks') {
            steps {
                // Executa o Gitleaks e gera o relatório gitleaks_report.json
                sh 'gitleaks detect --source=. --report-format=json --report-path=gitleaks_report.json || true'
            }
        }
        
        stage('Upload Reports to DefectDojo') {
            steps {
                // Envia os relatórios do Semgrep para o DefectDojo
                sh """
                    python3 /home/saynarah/.local/lib/python3.10/site-packages/defectdojo_api/defectdojo.py \
                        --url "${DEFECTDOJO_URL}" \
                        --api_key "${DEFECTDOJO_API_KEY}" \
                        --file semgrep_report.json \
                        --scan_type "Semgrep JSON Report" \
                        --product_name "Vampi"
                """

                // Envia os relatórios do Trivy para o DefectDojo
                sh """
                    python3 /home/saynarah/.local/lib/python3.10/site-packages/defectdojo_api/defectdojo.py \
                        --url "${DEFECTDOJO_URL}" \
                        --api_key "${DEFECTDOJO_API_KEY}" \
                        --file trivy_report.json \
                        --scan_type "Trivy Scan" \
                        --product_name "Vampi"
                """

                // Envia os relatórios do Gitleaks para o DefectDojo
                sh """
                    python3 /home/saynarah/.local/lib/python3.10/site-packages/defectdojo_api/defectdojo.py \
                        --url "${DEFECTDOJO_URL}" \
                        --api_key "${DEFECTDOJO_API_KEY}" \
                        --file gitleaks_report.json \
                        --scan_type "Gitleaks Scan" \
                        --product_name "Vampi"
                """
            }
        }

        stage('Sent Reports to DefectDojo') {
            steps {
                // Envia os relatórios do Semgrep para o DefectDojo
                sh '''
                    curl -X POST "${DEFECTDOJO_URL}" \
                      -H "Authorization: Token ${DEFECTDOJO_TOKEN}" \
                      -F "scan_type=Semgrep JSON Report" \
                      -F "product_id=1" \
                      -F "engagement=1" \
                      -F "file=@/home/saynarah/.jenkins/workspace/Projeto_de_segurança_trabalho_final/semgrep_report.json" \
                      -F "scan_date=$(date +%Y-%m-%d)" \
                      -F "active=true" \
                      -F "verified=true"
                '''
                
                // Envia os relatórios do Trivy para o DefectDojo
                sh '''
                     curl -X POST 'http://localhost:8080/api/v2/import-scan/' \
                      -H 'accept: application/json' \
                      -H 'Content-Type: multipart/form-data' \
                      -H 'Cookie: sessionid=4ffcc1e9fba5ee5815aadf12218367380e35373e' \
                      -H "Authorization: Token 21ec9553ffa670bc4e034073e5b39ca60084bf78" \
                      -F 'active=true' \
                      -F "verified=true" \
                      -F 'close_old_findings=false' \
                      -F 'test_title=' \
                      -F 'deduplication_on_engagement=true' \
                      -F 'push_to_jira=false' \
                      -F 'minimum_severity=Info' \
                      -F 'close_old_findings_product_scope=false' \
                      -F 'apply_tags_to_endpoints=true' \
                      -F "scan_date=$(date +%Y-%m-%d)" \
                      -F 'create_finding_groups_for_all_findings=true' \
                      -F "scan_date=$(date +%Y-%m-%d)" \
                      -F 'group_by=' \
                      -F 'version=' \
                      -F 'tags=' \
                      -F 'api_scan_configuration=' \
                      -F 'product_name=Vampi' \
                      -F "file=@/home/saynarah/.jenkins/workspace/Projeto_de_segurança_trabalho_final/trivy_report.json" \
                      -F 'auto_create_context=true' \
                      -F 'lead=' \
                      -F 'scan_type=Trivy Scan' \
                      -F 'branch_tag=string' \
                      -F 'source_code_management_uri=' \
                      -F 'engagement=1'
                   '''
                
                // Envia os relatórios do Gitleaks para o DefectDojo
                sh '''
                    curl -X POST "${DEFECTDOJO_URL}" \
                      -H 'accept: application/json' \
                      -H 'Content-Type: multipart/form-data' \
                      -H 'Cookie: sessionid=4ffcc1e9fba5ee5815aadf12218367380e35373e' \
                      -H "Authorization: Token 21ec9553ffa670bc4e034073e5b39ca60084bf78" \
                      -F 'active=true' \
                      -F "verified=true" \
                      -F 'close_old_findings=false' \
                      -F 'test_title=' \
                      -F 'deduplication_on_engagement=true' \
                      -F 'push_to_jira=false' \
                      -F 'minimum_severity=Info' \
                      -F 'close_old_findings_product_scope=false' \
                      -F 'apply_tags_to_endpoints=true' \
                      -F "scan_date=$(date +%Y-%m-%d)" \
                      -F 'create_finding_groups_for_all_findings=true' \
                      -F "scan_date=$(date +%Y-%m-%d)" \
                      -F 'group_by=' \
                      -F 'version=' \
                      -F 'tags=' \
                      -F 'api_scan_configuration=' \
                      -F 'product_name=Vampi' \
                      -F "file=@/home/saynarah/.jenkins/workspace/Projeto_de_segurança_trabalho_final/gitleaks_report.json" \
                      -F 'auto_create_context=true' \
                      -F 'lead=' \
                      -F 'scan_type=Gitleaks Scan' \
                      -F 'branch_tag=string' \
                      -F 'source_code_management_uri=' \
                      -F 'engagement=1'
                '''
            }
        }

        stage('Archive Artifacts') {
            steps {
                // Arquiva os relatórios JSON gerados
                archiveArtifacts artifacts: '**/*.json', allowEmptyArchive: true
            }
        }
    }

    post {
        always {
            // Passos que sempre acontecem após a execução do pipeline
            echo 'Pipeline finished'
        }

        success {
            // Ações quando o pipeline for bem-sucedido
            echo 'Pipeline was successful!'
        }

        failure {
            // Ações quando o pipeline falhar
            echo 'Pipeline failed'
        }
    }
}
