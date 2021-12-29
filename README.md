# HW-CICD-9.3

# Домашнее задание к занятию "09.03 CI\CD"

##  Подготовка к выполнению


Создаём 2 VM в yandex cloud со следующими параметрами: 2CPU 4RAM Centos7(остальное по минимальным требованиям)

+ done

Прописываем в inventory playbook'a созданные хосты

+ done

Добавляем в files файл со своим публичным ключом (id_rsa.pub). Если ключ называется иначе - найдите таску в плейбуке, которая использует id_rsa.pub имя и исправьте на своё

+ done

Запускаем playbook, ожидаем успешного завершения

+ done

Проверяем готовность Sonarqube через браузер

+ done

Заходим под admin\admin, меняем пароль на свой

+ done

Проверяем готовность Nexus через бразуер

+ done

Подключаемся под admin\admin123, меняем пароль, сохраняем анонимный доступ

+ done


# Знакомоство с SonarQube

##  Основная часть

1. Создаём новый проект, название произвольное

homework-9.3

2. Скачиваем пакет sonar-scanner, который нам предлагает скачать сам sonarqube

скачан

3. Делаем так, чтобы binary был доступен через вызов в shell (или меняем переменную PATH или любой другой удобный вам способ)

        vagrant@vagrant:~/sonar/sonar-scanner-4.6.2.2472-linux/bin$ export PATH=$PATH:$(pwd)

        vagrant@vagrant:~/sonar/sonar-scanner-4.6.2.2472-linux$ echo $PATH
        
        /home/vagrant/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:
        /snap/bin:/opt/elastic/7.10.1/bin:/opt/jdk        /11.0.13/bin:/opt/kibana/7.15.2/bin:/home/vagrant/sonar/sonar-scanner-4.6.2.2472-linux/bin
         vagrant@vagrant:

4. Проверяем sonar-scanner --version

        vagrant@vagrant:/etc/ansible/HW-9.3/09-ci-03-cicd/example$
        vagrant@vagrant:/etc/ansible/HW-9.3/09-ci-03-cicd/example$ pwd
        /etc/ansible/HW-9.3/09-ci-03-cicd/example
        vagrant@vagrant:/etc/ansible/HW-9.3/09-ci-03-cicd/example$
        vagrant@vagrant:/etc/ansible/HW-9.3/09-ci-03-cicd/example$
        vagrant@vagrant:/etc/ansible/HW-9.3/09-ci-03-cicd/example$ sonar-scanner --version
        INFO: Scanner configuration file: /home/vagrant/sonar/sonar-scanner-4.6.2.2472-linux/conf/sonar-scanner.properties
        INFO: Project root configuration file: NONE
        INFO: SonarScanner 4.6.2.2472
        INFO: Java 11.0.11 AdoptOpenJDK (64-bit)
        INFO: Linux 5.4.0-80-generic amd64
        vagrant@vagrant:/etc/ansible/HW-9.3/09-ci-03-cicd/example$


5. Запускаем анализатор против кода из директории example с дополнительным ключом -Dsonar.coverage.exclusions=fail.py

        sonar-scanner \
         -Dsonar.projectKey=homework-9.3 \
         -Dsonar.sources=. \
         -Dsonar.host.url=http://62.84.126.40:9000 \
         -Dsonar.login=534320383b80fda2ef52ee9fc0b78b8846b43cbe \
         -Dsonar.coverage.exclusions=fail.py


6.  Смотрим результат в интерфейсе

ошибки те же что и на вебинаре -- +=, index, pass

7.  Исправляем ошибки, которые он выявил(включая warnings)

исправлено

8.  Запускаем анализатор повторно - проверяем, что QG пройдены успешно

         vagrant@vagrant:/etc/ansible/HW-9.3/09-ci-03-cicd/example$ sudo sonar-scanner \
        >   -Dsonar.projectKey=homework-9.3 \
        >   -Dsonar.sources=. \
        >   -Dsonar.host.url=http://62.84.126.40:9000 \
        >   -Dsonar.login=534320383b80fda2ef52ee9fc0b78b8846b43cbe \
        >   -Dsonar.coverage.exclusions=fail.py
        INFO: Scanner configuration file: /home/vagrant/sonar/sonar-scanner-4.6.2.2472-linux/conf/sonar-scanner.properties
        INFO: Project root configuration file: NONE
        INFO: SonarScanner 4.6.2.2472
        INFO: Java 11.0.11 AdoptOpenJDK (64-bit)
        INFO: Linux 5.4.0-80-generic amd64
        INFO: User cache: /root/.sonar/cache
        INFO: Scanner configuration file: /home/vagrant/sonar/sonar-scanner-4.6.2.2472-linux/conf/sonar-scanner.properties
        INFO: Project root configuration file: NONE
        INFO: Analyzing on SonarQube server 9.1.0
        INFO: Default locale: "en_US", source code encoding: "UTF-8" (analysis is platform dependent)
        INFO: Load global settings
        INFO: Load global settings (done) | time=230ms
        INFO: Server id: 9CFC3560-AX4GuRQrRKzZOmdASUi1
        INFO: User cache: /root/.sonar/cache
        INFO: Load/download plugins
        INFO: Load plugins index
        INFO: Load plugins index (done) | time=110ms
        INFO: Load/download plugins (done) | time=253ms
        INFO: Process project properties
        INFO: Process project properties (done) | time=19ms
        INFO: Execute project builders
        INFO: Execute project builders (done) | time=6ms
        INFO: Project key: homework-9.3
        INFO: Base dir: /etc/ansible/HW-9.3/09-ci-03-cicd/example
        INFO: Working dir: /etc/ansible/HW-9.3/09-ci-03-cicd/example/.scannerwork
        INFO: Load project settings for component key: 'homework-9.3'
        INFO: Load project settings for component key: 'homework-9.3' (done) | time=116ms
        INFO: Load quality profiles
        INFO: Load quality profiles (done) | time=104ms
        INFO: Load active rules
        INFO: Load active rules (done) | time=2758ms
        WARN: SCM provider autodetection failed. Please use "sonar.scm.provider" to define SCM of your project, or disable the SCM Sensor in the project                settings.
        INFO: Indexing files...
        INFO: Project configuration:
        INFO:   Excluded sources for coverage: fail.py
        INFO: 1 file indexed
        INFO: Quality profile for py: Sonar way
        INFO: ------------- Run sensors on module homework-9.3
        INFO: Load metrics repository
        INFO: Load metrics repository (done) | time=92ms
        INFO: Sensor Python Sensor [python]
        WARN: Your code is analyzed as compatible with python 2 and 3 by default. This will prevent the detection of issues specific to python 2 or python 3.           You can get a more precise analysis by setting a python version in your configuration via the parameter "sonar.python.version"
        INFO: Starting global symbols computation
        INFO: 1 source file to be analyzed
        INFO: Load project repositories
        INFO: Load project repositories (done) | time=101ms
        INFO: 1/1 source file has been analyzed
        INFO: Starting rules execution
        INFO: 1 source file to be analyzed
        INFO: 1/1 source file has been analyzed
        INFO: Sensor Python Sensor [python] (done) | time=1001ms
        INFO: Sensor Cobertura Sensor for Python coverage [python]
        INFO: Sensor Cobertura Sensor for Python coverage [python] (done) | time=18ms
        INFO: Sensor PythonXUnitSensor [python]
        INFO: Sensor PythonXUnitSensor [python] (done) | time=4ms
        INFO: Sensor CSS Rules [cssfamily]
        INFO: No CSS, PHP, HTML or VueJS files are found in the project. CSS analysis is skipped.
        INFO: Sensor CSS Rules [cssfamily] (done) | time=5ms
        INFO: Sensor JaCoCo XML Report Importer [jacoco]
        INFO: 'sonar.coverage.jacoco.xmlReportPaths' is not defined. Using default locations: target/site/jacoco/jacoco.xml,target/site/jacoco-                         it/jacoco.xml,build/reports/jacoco/test/jacocoTestReport.xml
        INFO: No report imported, no coverage information will be imported by JaCoCo XML Report Importer
        INFO: Sensor JaCoCo XML Report Importer [jacoco] (done) | time=19ms
        INFO: Sensor C# Project Type Information [csharp]
        INFO: Sensor C# Project Type Information [csharp] (done) | time=4ms
        INFO: Sensor C# Analysis Log [csharp]
        INFO: Sensor C# Analysis Log [csharp] (done) | time=22ms
        INFO: Sensor C# Properties [csharp]
        INFO: Sensor C# Properties [csharp] (done) | time=0ms
        INFO: Sensor JavaXmlSensor [java]
        INFO: Sensor JavaXmlSensor [java] (done) | time=2ms
        INFO: Sensor HTML [web]
        INFO: Sensor HTML [web] (done) | time=13ms
        INFO: Sensor VB.NET Project Type Information [vbnet]
        INFO: Sensor VB.NET Project Type Information [vbnet] (done) | time=4ms
        INFO: Sensor VB.NET Analysis Log [vbnet]
        INFO: Sensor VB.NET Analysis Log [vbnet] (done) | time=19ms
        INFO: Sensor VB.NET Properties [vbnet]
        INFO: Sensor VB.NET Properties [vbnet] (done) | time=3ms
        INFO: ------------- Run sensors on project
        INFO: Sensor Zero Coverage Sensor
        INFO: Sensor Zero Coverage Sensor (done) | time=3ms
        INFO: SCM Publisher No SCM system was detected. You can use the 'sonar.scm.provider' property to explicitly specify it.
        INFO: CPD Executor Calculating CPD for 1 file
        INFO: CPD Executor CPD calculation finished (done) | time=12ms
        INFO: Analysis report generated in 158ms, dir size=102.9 kB
        INFO: Analysis report compressed in 31ms, zip size=13.9 kB
        INFO: Analysis report uploaded in 97ms
        INFO: ANALYSIS SUCCESSFUL, you can browse http://62.84.126.40:9000/dashboard?id=homework-9.3
        INFO: Note that you will be able to access the updated dashboard once the server has processed the submitted analysis report
        INFO: More about the report processing at http://62.84.126.40:9000/api/ce/task?id=AX4HF-N9mkeRiCHBgZhK
        INFO: Analysis total time: 7.157 s
        INFO: ------------------------------------------------------------------------
        INFO: EXECUTION SUCCESS
        INFO: ------------------------------------------------------------------------
        INFO: Total time: 9.287s
        INFO: Final Memory: 7M/49M
        INFO: ------------------------------------------------------------------------
        vagrant@vagrant:/etc/ansible/HW-9.3/09-ci-03-cicd/example$



9. Делаем скриншот успешного прохождения анализа, прикладываем к решению ДЗ

https://c2n.me/4eumvrS

https://c2n.me/4eumy2r

https://c2n.me/4eumDL0

https://c2n.me/4eumSK7


Знакомство с Nexus

Основная часть

1. В репозиторий maven-public загружаем артефакт с GAV параметрами:

        groupId: netology
        artifactId: java
        version: 8_282
        classifier: distrib
        type: tar.gz


группа maven-public включает в себя maven-release репозиторий

2. В него же загружаем такой же артефакт, но с version: 8_102

3. Проверяем, что все файлы загрузились успешно

https://c2n.me/4euobE2

4. В ответе присылаем файл maven-metadata.xml для этого артефекта

https://c2n.me/4euogWd

файл в репозитории

Знакомство с Maven

Подготовка к выполнению


1. Скачиваем дистрибутив с maven

https://dlcdn.apache.org/maven/maven-3/3.8.4/binaries/apache-maven-3.8.4-bin.zip

2. Разархивируем, делаем так, чтобы binary был доступен через вызов в shell (или меняем переменную PATH или любой другой удобный вам способ)

        export PATH=$PATH:$(pwd)

        vagrant@vagrant:~/maven/apache-maven-3.8.4/bin$ echo $PATH
         /home/vagrant/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:
         /snap/bin:/opt/elastic/7.10.1/bin:/opt/jdk/11.0.13/bin:/opt/kibana/7.15.2/bin:/home/vagrant/sonar/sonar-scanner-4.6.2.2472-                                  linux/bin:/home/vagrant/maven/apache-maven-3.8.4/bin
         vagrant@vagrant:~/maven/apache-maven-3.8.4/bin$



3. Удаляем из apache-maven-<version>/conf/settings.xml упоминание о правиле, отвергающем http соединение( раздел mirrors->id: my-repository-http-unblocker)

закоментировал

            <!--
            <mirror>
            <id>maven-default-http-blocker</id>
            <mirrorOf>external:http:*</mirrorOf>
            <name>Pseudo repository to mirror external repositories initially using HTTP.</name>
            <url>http://0.0.0.0/</url>
            <blocked>true</blocked>
            </mirror>
             -->



4. Проверяем mvn --version

        vagrant@vagrant:/etc/ansible/HW-9.3/09-ci-03-cicd/mvn$ mvn --version
        Apache Maven 3.8.4 (9b656c72d54e5bacbed989b64718c159fe39b537)
        Maven home: /home/vagrant/maven/apache-maven-3.8.4
        Java version: 11.0.13, vendor: Oracle Corporation, runtime: /opt/jdk/11.0.13
        Default locale: en_US, platform encoding: UTF-8
        OS name: "linux", version: "5.4.0-80-generic", arch: "amd64", family: "unix"
        vagrant@vagrant:/etc/ansible/HW-9.3/09-ci-03-cicd/mvn$

5. Забираем директорию mvn с pom

        vagrant@vagrant:/etc/ansible/HW-9.3/09-ci-03-cicd/mvn$ ls -l
        total 4
        -rw-r--r-- 1 root root 812 Dec 29 14:42 pom.xml
        vagrant@vagrant:/etc/ansible/HW-9.3/09-ci-03-cicd/mvn$


Основная часть

1. Меняем в pom.xml блок с зависимостями под наш артефакт из первого пункта задания для Nexus (java с версией 8_282)

2. Запускаем команду mvn package в директории с pom.xml, ожидаем успешного окончания

        [INFO] Building jar: /etc/ansible/HW-9.3/09-ci-03-cicd/mvn/target/simple-app-1.0-SNAPSHOT.jar
        [INFO] ------------------------------------------------------------------------
        [INFO] BUILD SUCCESS
        [INFO] ------------------------------------------------------------------------
        [INFO] Total time:  40.578 s
        [INFO] Finished at: 2021-12-29T18:28:23Z
        [INFO] ------------------------------------------------------------------------
        vagrant@vagrant:/etc/ansible/HW-9.3/09-ci-03-cicd/mvn$

3. Проверяем директорию ~/.m2/repository/, находим наш артефакт

        vagrant@vagrant:~/.m2/repository/netology/java/8_282$ ls -l
        total 164404
        -rw-rw-r-- 1 vagrant vagrant 168329081 Dec 29 18:27 java-8_282-distrib.tar.gz
        -rw-rw-r-- 1 vagrant vagrant        40 Dec 29 18:27 java-8_282-distrib.tar.gz.sha1
        -rw-rw-r-- 1 vagrant vagrant       350 Dec 29 18:26 java-8_282.pom
        -rw-rw-r-- 1 vagrant vagrant        40 Dec 29 18:26 java-8_282.pom.sha1
        -rw-rw-r-- 1 vagrant vagrant       199 Dec 29 18:27 _remote.repositories
        vagrant@vagrant:~/.m2/repository/netology/java/8_282$

4. В ответе присылаем исправленный файл pom.xml

в репозитории


