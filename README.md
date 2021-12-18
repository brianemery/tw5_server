# TW5 SERVER

Allows editing and saving of [TiddlyWiki](https://tiddlywiki.com) in a browser. Originally from https://gist.github.com/jimfoltz/ee791c1bdd30ce137bc23cce826096da by Jim Foltz.

## Installing / Getting started
First, download TiddlyWiki from https://tiddlywiki.com/empty.html and save it in a subfolder of this script but NOT as index.html. You can do this from the command line (e.g. Terminal on Mac) like so:

`/usr/bin/wget https://tiddlywiki.com/empty.html -P folder/`

If wget is unavailable, you can also use curl:

`curl https://tiddlywiki.com/empty.html >> folder/empty.html`

Then, to start the server, run the following:

`/usr/bin/ruby tw5-server.rb folder/empty.html`

If you want to host multiple TiddlyWikis, you can serve the whole subfolder, and then pick the desired file from a folder listing that will be automatically generated:

`/usr/bin/ruby tw5-server.rb folder`

## Securing your site
Suggest running this with a local firewall and/or proxy to secure external connections. The following is a working (but unprotected) proxy configuration for NGINX:

```nginx
upstream tiddlywiki.example.com {
        server 127.0.0.1:8000;
}

server {
        server_name tiddlywiki.example.com;
        client_max_body_size 20M;

        location / {
                proxy_pass http://127.0.0.1:8000;
                proxy_set_header Host $host;
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

* Fixed a trailing slash bug when serving a single TW file directly, expanded on the instructions, and added an example NGINX proxy configuration block.
  
  Stanimir Djevelekov, December 2021
