# OpenVPN Client Connect/Disconnect Script Plugin

Runs an external script in the background for client connect and disconnect events. The results are not used at all but also do not block the main openvpn process.

The idea of the plugin is to do as little as possible, and let the external binary do all the heavy lifting itself, and is built on top of https://github.com/fac/auth-script-openvpn but targeting client connect/disconnect rather than auth.

## Installation

Compile the shared library with `make plugin` and copy `openvpn-plugin-connect-disconnect-script.so` into your `lib/openvpn/plugins/` folder.

Copy your external script onto the machine in a sane place, making sure it's executable by the user openvpn is running as.

Configure the plugin in your openvpn config, passing the path to the external script as the second argument:

    plugin /path/to/openvpn-plugin-connect-disconnect-script.so /path/to/external/script.sh

The plugin will also pass any strings provided after the script name as arguments to the script execution:

    plugin /path/to/openvpn-plugin-connect-disconnect-script.so /path/to/external/script.sh arg1 arg2 argN

## External Script requirements

The script used to handle authentication has a very specific set of skills it needs, and if you don't provide those it will hunt you down in silence.

It needs to:

* Be executable by the user openvpn runs as
* Exit with status code 0
* Not depend on `PATH` variable (eg, don't use `/usr/bin/env` in shebang)

Example env the script is called in:

    PWD=/
    SHLVL=0
    auth_control_file=/tmp/openvpn_acf_9090e6750844ee26d7f23efbad0e95c2.tmp
    common_name=user2
    config=/opt/local/etc/openvpn/testvpn.conf
    daemon=1
    daemon_log_redirect=0
    daemon_pid=10502
    daemon_start_time=1488892554
    dev=tun0
    dev_type=tun
    ifconfig_local=192.168.2.1
    ifconfig_remote=192.168.2.2
    link_mtu=1572
    local_port_1=1194
    proto_1=tcp-server
    redirect_gateway=0
    remote_port_1=1194
    route_gateway_1=192.168.2.2
    route_net_gateway=172.18.42.1
    route_netmask_1=255.255.255.0
    route_network_1=192.168.2.0
    route_vpn_gateway=192.168.2.2
    script_context=init
    tun_mtu=1500
    trusted_ip=192.168.3.4
    trusted_port=54357
    untrusted_ip=192.168.3.4
    untrusted_port=54357
    username=a
    verb=9

Depending on the event triggering the script there will be additional env vars, for a client connect event:

    client_connect_deferred_file=/tmp/openvpn_ccr_45b4393855b104aa4ab382703adc488f.tmp
    client_connect_config_file=/tmp/openvpn_cc_21d0c97e1ad947ed155c405139ecf2a0.tmp

And for a client disconnect event:

    time_duration=4
    bytes_received=1018
    bytes_sent=3897

## License

See LICENSE.
