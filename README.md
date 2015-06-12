# Servator

A service creator based on the state of the running system. The idea is
to inspect a running system and create start scripts based on what is
found running.
It is so simple to create server scripts this way, example

    $ erl -sname foo -s foo -config foo.config
    > servator:make(foo).
    > ...
    > halt().
    
    $ copy etc/erlang/foo to /etc/erlang/foo
    $ copy var/erlang/foo to /var/erlang/foo
    $ # maybe edit /ets/erlang/foo.config to reflect the new home

The file layout created ( linux & mac ) is currently 

    /etc/erlang/<app>               ( config & script directory )
    /etc/erlang/<app>/start.args
    /etc/erlang/<app>/stop.args
    /etc/erlang/<app>/status.args
    /etc/erlang/<app>/attach.args
    /etc/erlang/<app>/<app>.run
    /etc/erlang/<app>/org.erlang.<app>.plist  ( max os x launch ctl file )
    /etc/erlang/<app>/<config>     ( applications config )

Log files and state information is stored here

    /var/erlang/<app>              ( home direcory for app )
    /var/erlang/app/log            ( log files )
    /var/erlang/app/rel/...        ( for releases ? )

## Commands

Starting the service is as simple as

    /etc/erlang/<app>/<app>.run start

To stop then 

    /etc/erlang/<app>/<app>.run stop

Attach to the running node and get a shell

    /etc/erlang/<app>/<app>.run attach

ctrl-C will only kill the client node.

To get current status

    /etc/erlang/<app>/<app>.run status

will report 'down' or 'up'

## Linux rc.local

To start an erlang service on most linux system when system starts
add the following line to /etc/rc.local ( before any exit 0 )

    # start <app> erlang service
    /etc/erlang/<app>/<app>.run start

## Mac OS X

On 10.10 ( and later ? )

    sudo chown root /etc/erlang/<app>/org.erlang.<app>.plist
    sudo launchctl bootstrap system /etc/erlang/<app>/org.erlang.<app>.plist
    sudo launchctl kickstart system/org.erlang.<app>.plist
    sudo launchctl enable system/org.erlang.<app>.plist

On 10.x before 10.10

    sudo launchctl load -w /etc/erlang/<app>/org.erlang.<app>.plist

Print current status (10.10 ?)

    launchctl print system/org.erlang.xylan
