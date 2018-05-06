pipeline {
  agent any
  environment {
    target_cluster = '10.65.179.12'
  }
  stages {
    stage('installation') {
      steps {
            sh 'python2.7 /var/tmp/Nightswatch/deploy/upgrade_machines_sa.py -p $target_cluster --ininame \'/var/lib/jenkins/nightswatch/5.1/config.ini\''
          }
    }

    stage('resiliency_with_machine_groups_npvr_5.1') {
      agent any
      steps {
        build (job: 'resiliency_with_machine_groups_npvr_5.1', propagate: false)
      }
    }

    stage('resiliency_with_machine_groups_rb_5.1') {
      agent any

      steps {
        build (job: 'resiliency_with_machine_groups_rb_5.1', propagate: false)
      }
    }

    stage('copy xmls') {
      steps {
        sh '''scp -p root@$target_cluster:/tmp/ingest_resiliency/*.xml .'''
      }
    }

    stage('check_cores') {
      steps {
        sh 'ssh root@$target_cluster "python2.7 /var/Nightswatch/deploy/find_cores.py"'
      }
    }

    stage('publish junit results') {
      steps {
        junit(testResults: '*.xml', healthScaleFactor: 1.0, allowEmptyResults: true)
      }
    }

  }
}