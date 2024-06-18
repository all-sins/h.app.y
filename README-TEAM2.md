# Add following line to OS hosts file to emulate deployment.
127.0.0.1      h.app.y

# Generate self-signed certificate. (Ran out of time. Not implemented. HTTP instead.)
openssl req -newkey rsa:2048 -nodes -keyout server.key -out server.csr
openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
cat server.key server.crt > server.pem

# Use authbind to run webserver on port 80 so that the domain name can be called cleanly.
sudo apt-get install authbind
sudo touch /etc/authbind/byport/80
sudo chown $(whoami) /etc/authbind/byport/80
sudo chmod 755 /etc/authbind/byport/80
authbind --deep npm start

# Config webpack dev server. (webpack.config.js)
const path = require('path');
const fs = require('fs');

module.exports = {
  // other configurations...
  devServer: {
    host: 'h.app.y',
    port: 443, // HTTPS default port
    https: {
      key: fs.readFileSync(path.resolve(__dirname, 'server.key')),
      cert: fs.readFileSync(path.resolve(__dirname, 'server.crt')),
    },
    allowedHosts: [
      'h.app.y'
    ],
  },
};

# Access application with http://h.app.y

