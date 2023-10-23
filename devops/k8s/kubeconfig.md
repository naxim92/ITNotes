#k8s 

- Как получить полный конфиг с открытыми секретами из конфига со ссылками на файлы сертификатов и тд? Например из kubeconfig от minikube
	`kubectl config view --flatten=true`