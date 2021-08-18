## TW5 SERVER
Originally from:
https://gist.github.com/jimfoltz/ee791c1bdd30ce137bc23cce826096da

Allows editing and saving of tiddlywiki (https://tiddlywiki.com) in a browser.

USAGE
From the command line (e.g. Terminal on Mac):
/usr/bin/ruby tw5-server.rb /folder

MODIFICATIONS
Added a bind address in the server definition:
server = WEBrick::HTTPServer.new({:Port => 8000, :DocumentRoot => root, :BindAddress => "127.0.0.1"})

This will prevent webbrick from accepting connections from remote hosts (a portscan with nmap 
suggests this is true), and exposing your file system to the internet. I also suggest running 
this with a local firewall to prevent external connections.

Brian Emery, August 2021
