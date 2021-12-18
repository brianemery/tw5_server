# TW5 SERVER

Allows editing and saving of [TiddlyWiki](https://tiddlywiki.com) in a browser. Originally from https://gist.github.com/jimfoltz/ee791c1bdd30ce137bc23cce826096da by Jim Foltz.

## Installing / Getting started
First, download TiddlyWiki from https://tiddlywiki.com/empty.html and save it in a subfolder of this script but NOT as index.html. You can do this from the command line (e.g. Terminal on Mac) like so:

`curl https://tiddlywiki.com/empty.html >> folder/empty.html`

Then, to start the server, run the following:

`ruby tw5-server.rb folder/empty.html`

If you want to host multiple TiddlyWikis, you can serve the whole subfolder, and then pick the desired file from a folder listing that will be automatically generated:

`ruby tw5-server.rb folder`

### Example systemd service

You can automate your TiddlyWiki server with a systemd service:

```
[Unit]
Description=TiddlyWiki
After=network.target

[Service]
Type=simple
WorkingDirectory=/home/$USER/twiki
ExecStart=/usr/bin/ruby tw5-server.rb folder/empty.html
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

## Serving and securing your site
We suggest running this with a local firewall and/or proxy to secure external connections. The following is a working proxy configuration for NGINX with optional password protection:

```nginx
upstream tiddlywiki.example.com {
        server 127.0.0.1:8000;
}

server {
        server_name tiddlywiki.example.com;
        client_max_body_size 20M;

        location / {
                #uncomment these lines to enable password protection after you have set up your user and password
                #as per https://docs.nginx.com/nginx/admin-guide/security-controls/configuring-http-basic-authentication/
                #
                #auth_basic "Restricted";
                #auth_basic_user_file /etc/nginx/htpasswd;

                proxy_pass http://127.0.0.1:8000;
                proxy_set_header Host $host;
                proxy_set_header     X-Real-IP       $remote_addr;
                proxy_set_header     X-Forwarded-For $proxy_add_x_forwarded_for;
        }
}
```

## Modifications to the original script
* Added a bind address in the server definition:
  ```server = WEBrick::HTTPServer.new({:Port => 8000, :DocumentRoot => root, :BindAddress => "127.0.0.1"})```

  This will prevent webbrick from accepting connections from remote hosts (a portscan with nmap 
  suggests this is true), and exposing your file system to the internet. I also suggest running 
  this with a local firewall to prevent external connections.

  Brian Emery, August 2021

* Fixed a trailing slash bug when serving a single TW file directly, expanded on the instructions, and added an example NGINX proxy configuration and a systemd service.
  
  Stanimir Djevelekov, December 2021
