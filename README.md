Some useful scripts I made to help me configure wireguard. may require some editing to fit your individual circumstances, but I am trying to make them more adaptable.

This does require wireguard to be installed and I have been running/testing with Debian 11 as my main server and Rocky Linux 8.7 as my client.
first run addserver as root with the tunnel ip and subnet in CIDR format

sudo ./addserver 172.16.0.1/24

you can optionally specify a listen port, otherwise it defaults to 51820

sudo ./addserver 172.16.0.1/24 51000

This will create a wg0.conf file, your tunnel file and public key file, and then enable the service.
After that create peers by creating the following: a subdirectory for the peer files, peernet (a comma separated CIDR list of networks the client that can access the server), and peeraddr (a CIDR address for the tunnel. This should be in the same subnet as the tunnel address you specified in the server creation)

mkdir remote-system

cd remote-system

echo "172.16.0.2/24" > peeraddr

echo "192.168.0.0/24" > peernet

cd ..

./genpeer remote-system

genpeer will create the public key, private key and a config file. Then you can add the remote peer to the server config

sudo ./addpeer remote-system

this will read the remote info and add it into the server config and start wireguard on the server.
You just need to move the config file and put it on the remote system as /etc/wireguard/wg0.conf before we start the peer connection to wireguard with

systemctl start wg-quick@wg0

if you want it to run on every startup you need to enable it

systemctl enable wg-quick@wg0

to check the status of the tunnel you can run 'wg show'
