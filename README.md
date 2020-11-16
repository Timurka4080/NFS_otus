# Инструкции
Для проверки надо скачать репозиторий 

	cd ~
    git clone git@github.com:Timurka4080/NFS_otus.git
	
после этого зайдите в директорию NFS_otus и запускаем виртуальные машины с помощью vagrant

	cd ~/NFS_otus/
	vagrant up 
 
## Описание скрипта  nfss_script.sh
	sudo su
	yum update -y
	systemctl enable nfs && echo 'nfs enable'
	systemctl start nfs && echo 'nfs start' # включаем и стартуем демон NFS
	mkdir /usr/local/nfs # создаем директорию для монтирования 
	echo '/usr/local/nfs              *(rw,root_squash)' > /etc/exports  # передаем путь до директории и параметры монтирования 

	exportfs -r   # применяем изменения 
	systemctl enable firewalld  
	systemctl start firewalld    # запускам и стартуем демон firewalld
	firewall-cmd --permanent --add-port=111/tcp       # открываем ножные порты 
	firewall-cmd --permanent --add-port=54302/tcp
	firewall-cmd --permanent --add-port=20048/tcp
	firewall-cmd --permanent --add-port=2049/tcp
	firewall-cmd --permanent --add-port=46666/tcp
	firewall-cmd --permanent --add-port=42955/tcp
	firewall-cmd --permanent --add-port=875/tcp
	firewall-cmd --permanent --add-port=111/udp
	firewall-cmd --permanent --add-port=54302/udp
	firewall-cmd --permanent --add-port=20048/udp
	firewall-cmd --permanent --add-port=2049/udp
	firewall-cmd --permanent --add-port=46666/udp
	firewall-cmd --permanent --add-port=42955/udp
	firewall-cmd --permanent --add-port=875/udp
	firewall-cmd --permanent --zone=public --add-service=nfs
	firewall-cmd --permanent --zone=public --add-service=mountd
	firewall-cmd --permanent --zone=public --add-service=rpc-bind
	firewall-cmd --reload       # перечитываем конфигурацию 
	mkdir /usr/local/nfs/upload  #Создаем папку для Шары
	chmod 777 /usr/local/nfs/upload  # даем для общей папки права для всех пользователей 

## Описание скрипта  nfsc_script.sh

	sudo su
	yum update -y
	sed -ie '/^UUID/a 192.168.50.10:/usr/local/nfs /mnt nfs rw,vers=3,sync,proto=udp,noauto,rsize=32768,wsize=32768 0 0' /etc/fstab
	''' Так как у меня vagrant создает запись в конце файла /etc/fstab приходится вставлять строчку с параметрами монтируемой директории и ключами для NFS клиента в середину файла '''
	systemctl restart remote-fs.target # перезагружаем сервис для применения настроек
