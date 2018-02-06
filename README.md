# fhem-landroid-s
Worx Landroid-S Module for FHEM SmartHome

![Logo](images/Fhem_Landroid_Adapter.jpg)

## Change Log
```
- 1.0 (23.09.17): Receive Readings from Landroid S Mower
- 1.1 (25.11.17): set Mower commands and change configuration is now supported
```
Installation Guide on Raspberry PI (Debian)

## 1. Install Node.js (min. Node 4.X.X)

  https://www.einplatinencomputer.com/raspberry-pi-node-js-installieren/

  If you have the following error after Node.js installation (ARMV61)
  node: /usr/lib/arm-linux-gnueabihf/libstdc++.so.6: version `GLIBCXX_3.4.18' not found (required by node)

  https://askubuntu.com/questions/575505/glibcxx-3-4-20-not-found-how-to-fix-this-error

  Run:
  sudo apt-get install libstdc++6



## 2. Install FHEM Landroid-S Adapter
  
  - Switch to directory
  
    $ cd /opt
    
  - Create new folder
  
      sudo mkdir landroid
  
  - Change Owner and rights (Same like your login user)
  
      $ sudo chown (YOUR LOGIN USER) landroid
      
      $ sudo chmod 777 landroid
 
  - Switch to directory
  
    $ cd landroid
    
  - Download fhem-landroid-s package from GIT
  
    $ git clone https://github.com/axelmohnen/fhem-landroid-s.git
    
  - Switch directory

    $ cd fhem-landroid-s
    
  - Install ioBroker Landroid adapter
  
    $ sudo npm install iobroker.landroid-s
    
  - Copy Landroid perl module to FHEM
    
    $ cp 74_LANDROID.pm /opt/fhem/FHEM
    
  - Change owner and group of module like for the other modules in the same folder (FHEM User and Group)
  
    $ sudo chown (FHEM USER) /opt/fhem/FHEM/74_LANDROID.pm
    
    $ sudo chgrp (FHEM GROUP) /opt/fhem/FHEM/74_LANDROID.pm
    
  - Edit Landroid Adapter config File (Worx Login data)
    
    $ nano LandroidConf.json
    
      Change email address
      
      Change password
      
      Press CTRL-o  to save changes
      
      Press enter to confirm filename to write
      
      Press CTRL-x to exit nano
      
  - Execute node server for testing (exit afterwards with CTRL-C)
  
    $ node LandroidSrv.js
    
    Result:
    
      get  https://pastebin.com/raw/xD6ZPULZ ->  200
      
      get  https://www.symantec.com/content/en/us/enterprise/verisign/roots/VeriSign-Class%203-Public-Primary-Certification-Authority-G5.pem ->  200
      
      Wed, 18 Oct 2017 19:10:23 GMT --> Connected to mower
      
      Wed, 18 Oct 2017 19:10:23 GMT --> Landroid WebServer: server initialized
      
      Wed, 18 Oct 2017 19:10:23 GMT --> Landroid WebServer: server running

## 3. Install FHEM Module 74_LANDROID

  - Install Perl JSON Module
  
    $ sudo apt-get install libjson-pp-perl
    
  -	Define Landroid device in FHEMWEB. Use "localhost" as host if the LandroidSrv10.js is running on same machine. Otherwise choose the     IP address of external server. The default port is 8001 and must not be defined.
  
    define robbi LANDROID localhost
    
    Press "Save config"
    
    Set attribute “disable” equal 0 (default = 1)

    Press "Save config"
    
    Default "interval" is set to 180sec
    
    


## 4. Setup process manager for Node.js applications

  - Install Process manager
  
    $ npm install pm2 -g
  
  - Start your landroid server in background
  
    $ pm2 start /opt/landroid/fhem-landroid-s/LandroidSrv.js
  
  - Check Process Monitoring (your landroid server must be up and running before continuing)
  
    $ pm2 list 
    
    or
    
    $ pm2 show LandroidSrv (for more details)
  
  - Setup Startup/Boot management
  
    $ pm2 startup
    
    copy/paste and execute given command in order to build startup script
    
    $ pm2 save
  
  - Restart PI
  
    $ sudo shutdown -r now
 
 ## 5. Set commands
  - startMower: Start Mower (no parameter needed)
  
  - stopMower: Going back to docking station (no parameter needed)
  
  - changeCfgCalendar: Set calendar per weekday
  ```
    	Example: 0,10:00,300,1
      		- weekday 0-6 (0 = Sunday, 6 = Saturday)
      		- starttime (hh:mm) (e.g 10:00)
      		- worktime in minutes (value between 0 and 720)
      		- bordercut (1 = bordercut on, 0 = bordercut off)
  ```
		
  - changeCfgTimeExtend: Set mowing time extension (Percentage value -100 to 100)

  - changeCfgArea: Set mowing area (Zone)	
  ```
  	Example: 0,450
			- Area ID 0-3 (0 = Area 1, 3 = Area 4)
			- Starting Point in meters 0 - 500
  ```
  
  - startSequences: Start sequence (up to 10 sequences possible)
  ```
  	Example: 0,0,0,0,0,0,0,0,0,0
			- Sequence ID 0-3 (? -> Please let me know if you know it!)
  ```
  
  - changeRainDelay: Set Rain delay in minutes (value between 0 and 300)
  

