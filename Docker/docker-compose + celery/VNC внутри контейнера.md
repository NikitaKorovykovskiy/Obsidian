```
apt-get update && apt-get install -y tigervnc-standalone-server
apt-get install -y xfce4 xfce4-goodies
echo "startxfce4 &" >> ~/.vnc/xstartup
chmod +x ~/.vnc/xstartup
touch /root/.Xauthority
vncpasswd
```
```
vncserver :0
```
