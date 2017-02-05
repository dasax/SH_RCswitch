# SH_RCswitch
SH_RCswithc is a plugin for smarthomeNG to send RC switch commands. With this plugin 433mhz remote controlled power plugs can be controlled from the smarthomeNG environment.

## Necessary Hardware
- RaspberryPi or any other board wich has digital GPIO
- [433 Mhz transmitter](https://www.google.de/search?q=433+mhz+transmitter&client=opera&hs=aeh&source=lnms&tbm=isch&sa=X&ved=0ahUKEwjzsYKo7vHRAhXKWxoKHdk1D6YQ_AUICSgC&biw=1163&bih=589)
- 433 Mhz controlled power plug, e.g. Brennenstuhl RCS 1000 N

Connect the VCC of the 433Mhz transmitter to any 5V output pin of your board, the GND to a ground pin and the ATAD to any GPIO pin. In this example we use  pin 17. I reccomend also to connect a (long) cable to the ANT pin of the 433Mhz transmitter - this extends the range of the sender.

## Installation of 3rd party software
The plugin depends on two 3rd party software packages:
- wiringPi
- rcswitch-pi

### Installation of wiringPi:
In case not already done, update the system and install git:
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get install git-core
Then download wiringPi to /usr/local/bin/wiringPi
    cd /usr/local/bin
    sudo git clone git://git.drogon.net/wiringPi
and install it:
    cd wiringPi
    sudo ./build

>Soure: https://raspiprojekt.de/machen/basics/software/10-wiringpi.html?showall=&start=1

### Installation of rcswitch-pi
Download the sources into /etc/local/bin/rcswitch-pi:
  cd /usr/local/bin
  sudo git clone https://github.com/r10r/rcswitch-pi.git
Before building rcswitch-pi, the port has to be defineded the the code has to be slightly changed. Therefore edit the file send.cpp and the change the port to your needs and replace the command wiringPiSetup() to wiringPiSetupSys(). For editing the file:
  cd rcswitch-pi
  sudo nano send.cpp
In our example the file send.cpp has to look like follwos:
  int PIN = 17;
  char* systemCode = argv[1];
  int unitCode = atoi(argv[2]);
  int command = atoi(argv[3]);

  if (wiringPiSetupSys() == -1) return 1;
     printf("sending systemCode[%s] unitCode[%i] command[%i]\n", systemCode, unitCode, command);
     RCSwitch mySwitch = RCSwitch();
     mySwitch.enableTransmit(PIN);

  switch(command) {
      case 1:
          mySwitch.switchOn(systemCode, unitCode);
          break;
      case 0:
          mySwitch.switchOff(systemCode, unitCode);
          break;
      default:
Save the file (ctrl + o) and leave nano (ctrl+x)
Now rcswitch pi can be compiled:
  cd rcswitch-pi
  make

> source https://raspiprojekt.de/anleitungen/schaltungen/28-433-mhz-funksteckdosen-schalten.html?showall=&start=1
