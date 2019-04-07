Use Supervisor to run tcpdump during docker build
Then gratutious running of tests, healthchecks and commands

To perform a tcpddump during a build of a different application, change the
files in phantomjs/files/etc/supervisord.d/

To better inspect the results of tcpdump do something like:

docker compose up --build phantomjs | tee log.txt

