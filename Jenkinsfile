pipeline {
    agent any

	parameters {
		choice(name: 'ENV', choices: ['dev','rc','prod'], description: 'Choisisez environnement de déploiement')
        string(name: 'SERVER_HOSTNAME', defaultValue: 'pdns.dev.local.ca', description: 'Le nom du serveur sur lequel déployer les certificats')
		string(name: 'SERVER_USER', defaultValue: 'vagrant', description: 'Le nom de l\'utilisateur du serveur')
		password(name: 'SERVER_PASSWORD', defaultValue: 'vagrant', description: 'Le mot de passe du serveur')
		string(name: 'PDNS_SERVER_IP', defaultValue: '192.168.56.66', description: 'L''adresse IP du serveru pdns')
		string(name: 'PDNS_DOMAINE', defaultValue: 'dev.local.ca', description: 'Le domaine à créer dans le serveur pdns')
        choice(name: 'ANSIBLE_VM_HOSTNAME', choices: ['ansible.dev.local.ca'], description: 'Le hostname du serveur ansible')
        choice(name: 'ANSIBLE_VM_USER', choices: ['vagrant'], description: 'Le user du serveur ansible')
        password(name: 'ANSIBLE_VM_PASSWORD', defaultValue: 'vagrant', description: 'Le mot de passe du serveur ansible')
	}

    stages {
	stage('Build') {
	    steps { 
			sh 'ls'
			echo 'Building...'
			echo 'Generate 00_inventory.yml file'
            sh '''
cat > ./ansible/env/${ENV}/00_inventory.yml <<EOF
all:
  hosts:
    ${SERVER_HOSTNAME}:
      ansible_host: ${SERVER_HOSTNAME}
      ansible_user: ${SERVER_USER}
      ansible_ssh_pass: ${SERVER_PASSWORD}
  vars:
    ansible_python_interpreter: /usr/bin/python3
    ansible_ssh_common_args: '-o StrictHostKeyChecking=no'
EOF
'''
			echo 'Generate vars.yml file'
			sh '''
cat > ./ansible/env/${ENV}/group_vars/all/vars.yml <<EOF
# Variable globale pdns
pdns_servername: ${SERVER_HOSTNAME}
pdns_pdns_ip: ${PDNS_SERVER_IP}
pdns_domaine: ${POWERDNS_DOMAINE}
roles_path: ~/.ansible/roles
EOF
'''
			echo 'Copying files...'
    		sh 'sshpass -p ${ANSIBLE_VM_PASSWORD} ssh -o \'StrictHostKeyChecking=no\' ${ANSIBLE_VM_USER}@${ANSIBLE_VM_HOSTNAME} "echo -e \'${ANSIBLE_VM_PASSWORD}\n\' | sudo -S rm -rf deploy_powerdns"'
    		sh 'sshpass -p ${ANSIBLE_VM_PASSWORD} ssh -o \'StrictHostKeyChecking=no\' ${ANSIBLE_VM_USER}@${ANSIBLE_VM_HOSTNAME} "mkdir deploy_powerdns"'
			sh 'sshpass -p ${ANSIBLE_VM_PASSWORD} scp -o \'Stricthostkeychecking=no\' -r ./ansible ${ANSIBLE_VM_USER}@${ANSIBLE_VM_HOSTNAME}:deploy_powerdns/'
	        sh 'echo "Set config completed !."'
	    }
	}

	stage('Deploy') {
	    steps {
			sh 'echo "Begin deploying..."'
	        sh 'sshpass -p ${ANSIBLE_VM_PASSWORD} ssh -o \'StrictHostKeyChecking=no\' ${ANSIBLE_VM_USER}@${ANSIBLE_VM_HOSTNAME} "cd deploy_powerdns/ansible && ansible-playbook -i env/${ENV} playbook.yml -v"'
	        sh 'echo "Deploying completed !!"'
	    }
	}
	stage('Test') {
	    steps {
			sh 'echo "Testing server http://${SERVER_HOSTNAME}:8080...."'
	        sh 'sshpass -p ${ANSIBLE_VM_PASSWORD} ssh -o \'StrictHostKeyChecking=no\' ${ANSIBLE_VM_USER}@${ANSIBLE_VM_HOSTNAME} "cd deploy_powerdns/ansible && ansible-playbook -i env/${ENV} ./playbook/testing.yml -v"'
	        sh 'echo "Test server http://${SERVER_HOSTNAME}:8080 completed !!"'
	    }
	}
    }
}