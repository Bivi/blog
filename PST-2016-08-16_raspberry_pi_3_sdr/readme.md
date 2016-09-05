# Post
![](viewmex.jpg)

## Name
[`PST-2016-08-16_raspberry_pi_3_sdr`]()

## Title
Exploitation d'un dongle RTL-SDR avec une Raspberry Pi 3

## Description

### Remise en route du Raspberry Pi 3

Découverte de l'adresse IP, depuis la fenêtre terminale lancée sur le petit écran et un clavier USB.

~~~~sh
pi@raspberrypi ~ $ ifconfig
~~~~

Connexion ssh sur le compte principal `pi` depuis le PC avec ssh sous Git BASH par exemple.

~~~~sh
ssh pi@192.168.0.49
pi@192.168.1.49's password: raspberry
~~~~

Lancer un server vnc (login pi/raspberry)

~~~~sh
vncserver :1 -geometry 1024x768 -depth 24
~~~~

#### Mise à jour du système

~~~~sh
sudo apt-get update
sudo apt-get upgrade
sudo rpi-update
~~~~
=> avec rpi-update le firmware passe de   Linux raspberrypi 4.1.19-v7+ #858  à   rpi-4.4.y

Et reboot ! ... perte écran LCD :-(

#### Reconfiguration écran LCD

Atteindre le wiki Waveshare http://www.waveshare.com/wiki/3.5inch_RPi_LCD_\(A\) et télécharger la dernière version des drivers : http://www.waveshare.com/w/upload/3/3d/LCD-show-160811.tar.gz

Extraire l'archive dans `/home/pi/LCD-show` et :

~~~~sh
cd LCD-show
./LCD35-show
~~~~

### Installation Dongle SDR
D'après l'article *Hackable n°2 p44*

D'après https://www.framboise314.fr/ecoutez-la-radio-avec-votre-raspberry-pi/

Il s'agit ici d'un dongle **NooElec R820T2 SDR & DVB-T NESDR Mini 2+**

~~~~sh
pi@raspberrypi:~ $ sudo apt-get -y install rtl-sdr
~~~~

Premier test

~~~~sh
pi@raspberrypi:~ $ rtl_test
Found 1 device(s):
  0:  Realtek, RTL2838UHIDIR, SN: 00000001

Using device 0: Generic RTL2832U OEM
Found Rafael Micro R820T tuner
Supported gain values (29): 0.0 0.9 1.4 2.7 3.7 7.7 8.7 12.5 14.4 15.7 16.6 19.7 20.7 22.9 25.4 28.0 29.7 32.8 33.8 36.4 37.2 38.6 40.2 42.1 43.4 43.9 44.5 48.0 49.6
Sampling at 2048000 S/s.

Info: This tool will continuously read from the device, and report if
samples get lost. If you observe no further output, everything is fine.

Reading samples in async mode...
^CSignal caught, exiting!

User cancel, exiting...
Samples per million lost (minimum): 0
~~~~

Essai "France Inter" 87.7 MHz

~~~~sh
rtl_fm -f 87.7M -M wbfm -s 400k -r 32k | aplay -r 32k
~~~~
...bof

Installation de GNUradio

~~~~sh
sudo apt-get install gnuradio gnuradio-dev
~~~~








~~~~sh
pi@raspberrypi:~ $ dmesg
    ...
[ 7465.658889] usb 1-1.5: new high-speed USB device number 5 using dwc_otg
[ 7465.771606] usb 1-1.5: New USB device found, idVendor=0bda, idProduct=2838
[ 7465.771630] usb 1-1.5: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[ 7465.771643] usb 1-1.5: Product: RTL2838UHIDIR
[ 7465.771655] usb 1-1.5: Manufacturer: Realtek
[ 7465.771668] usb 1-1.5: SerialNumber: 00000001
[ 7465.858988] usb 1-1.5: dvb_usb_v2: found a 'Realtek RTL2832U reference design' in warm state
[ 7465.913813] usb 1-1.5: dvb_usb_v2: will pass the complete MPEG2 transport stream to the software demuxer
[ 7465.913875] DVB: registering new adapter (Realtek RTL2832U reference design)
[ 7465.935676] i2c i2c-3: Added multiplexed i2c bus 4
[ 7465.935699] rtl2832 3-0010: Realtek RTL2832 successfully attached
[ 7465.935735] usb 1-1.5: DVB: registering adapter 0 frontend 0 (Realtek RTL2832 (DVB-T))...
[ 7465.948708] r820t 4-001a: creating new instance
[ 7465.960274] r820t 4-001a: Rafael Micro r820t successfully identified
[ 7465.971396] Registered IR keymap rc-empty
[ 7465.971839] input: Realtek RTL2832U reference design as /devices/platform/soc/3f980000.usb/usb1/1-1/1-1.5/rc/r                c0/input2
[ 7465.972091] rc0: Realtek RTL2832U reference design as /devices/platform/soc/3f980000.usb/usb1/1-1/1-1.5/rc/rc0
[ 7465.979206] IR RC5(x/sz) protocol handler initialized
[ 7465.981038] IR NEC protocol handler initialized
[ 7465.981447] IR RC6 protocol handler initialized
[ 7465.985531] IR JVC protocol handler initialized
[ 7465.992114] usb 1-1.5: dvb_usb_v2: schedule remote query interval to 200 msecs
[ 7465.992242] IR Sony protocol handler initialized
[ 7465.994163] input: MCE IR Keyboard/Mouse (dvb_usb_rtl28xxu) as /devices/virtual/input/input3
[ 7465.996365] IR MCE Keyboard/mouse protocol handler initialized
[ 7465.996384] IR Sharp protocol handler initialized
[ 7465.996922] IR SANYO protocol handler initialized
[ 7465.998690] lirc_dev: IR Remote Control driver registered, major 243
[ 7466.001793] IR XMP protocol handler initialized
[ 7466.001876] usb 1-1.5: dvb_usb_v2: 'Realtek RTL2832U reference design' successfully initialized and connected
[ 7466.002140] usbcore: registered new interface driver dvb_usb_rtl28xxu
[ 7466.003191] rc rc0: lirc_dev: driver ir-lirc-codec (dvb_usb_rtl28xxu) registered at minor = 0
[ 7466.003212] IR LIRC bridge handler initialized
    ...
~~~~

Les Ids `idVendor=0bda, idProduct=2838` indiquent bien un device ezcap **USB 2.0 DVB-T/DAB/FM dongle**.

Le message suivant indique que le dongle à été reconnu automatiquement

~~~~sh
`usbcore: registered new interface driver dvb_usb_rtl28xxu` indique que 
~~~~

Désactivation du module :

~~~~sh
pi@raspberrypi:~ $ lsmod
Module                  Size  Used by
    ...
r820t                  16456  0
rtl2832                10737  0
i2c_mux                 2273  1 rtl2832
dvb_usb_rtl28xxu       15616  0
dvb_usb_v2             13077  1 dvb_usb_rtl28xxu
dvb_core               80746  2 rtl2832,dvb_usb_v2
rc_core                16220  13 ir_sharp_decoder,ir_xmp_decoder,lirc_dev,ir_lirc_codec,dvb_usb_rtl28xxu,ir_rc5_decoder,ir_nec_decoder,ir_sony_decoder,ir_mce_kbd_decoder,ir_jvc_decoder,dvb_usb_v2,ir_rc6_decoder,ir_sanyo_decoder
    ...

pi@raspberrypi:~ $ sudo nano /etc/modprobe.d/raspi-blacklist.conf
pi@raspberrypi:~ $ cat /etc/modprobe.d/raspi-blacklist.conf
blacklist dvb_usb_rtl28xx
blacklist rtl2832
blacklist rtl2830

~~~~