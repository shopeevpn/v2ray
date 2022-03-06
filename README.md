# v2ray
Configuration Overview
V2Ray shares a same structure of configuration between server side and client side shown as below. Server and client configurations are different in specific sections.

Below is the top level structure of the configuration. Each section has its own format.

```
{
  "log": {},
  "api": {},
  "dns": {},
  "stats": {},
  "routing": {},
  "policy": {},
  "inbounds": [],
  "outbounds": [],
  "transport": {}
}
```
log: LogObject

Log configuration to control log outputs.

api: ApiObject

RPC API to control the V2Ray instance. See API configuration for details.

dns: DnsObject

Configuration for internal DNS server's configurations. If this section is omitted, V2Ray will use your system-wide DNS configuration. For details, see DNS Configurations.

stats: StatsObject

When specified, internal Statistics is enabled.

policy: PolicyObject

Configurations for permissions and other security strategies. For details, see Local Policy.

routing: RoutingObject

Configuration for internal Routing strategy.

inbounds: [ InboundObject ]

An array of InboundObject as configuration for inbound proxies.

outbounds: [ OutboundObject ]

An array of OutboundObject as configuration for outbound proxies. The first outbound in the array is the main one. It is the default outbound in routing decision.

transport: TransportObject

Low-level transport protocol's configurations. For details, see Protocol Transport Options.

LogObject
```
{
  "access": "/path/to/file",
  "error": "/path/to/file",
  "loglevel": "warning"
}
```
access: string

Path to access log. If not empty, it must be a legal file path, such as "/tmp/v2ray/_access.log"(Linux), or "C:\\Temp\\v2ray\\_access.log"(Windows). If empty, V2Ray writes access log to stdout.

error: string

Path to error log. If not empty, it must be a legal file path. If empty, V2Ray writes error log to stdout.

loglevel: "debug" | "info" | "warning" | "error" | "none"

Level of logs to be written. Different log levels indicate different content of logs. Default value is "warning".

Log levels:

"debug": Information for developers only. Also includes all "info" logs.
"info": Information for current state of V2Ray. Users don't have to take care of those. Also includes all "warning" logs.
"warning": Something wrong with the environment, usually outside of V2Ray, e.g., network breakage. V2Ray still runs, but users may experience some breakages. Also includes all "error" logs.
"error": Something severely wrong, that V2Ray can't run at all.
"none": All logging are disabled.
InboundObject
An InboundObject defines an inbound proxy. It handles incoming connections to V2Ray. Available proxies are listed here.
```
{
  "port": 1080,
  "listen": "127.0.0.1",
  "protocol": "protocol_name",
  "settings": {},
  "streamSettings": {},
  "tag": "inbound_tag_name",
  "sniffing": {
    "enabled": false,
    "destOverride": ["http", "tls"]
  },
  "allocate": {
    "strategy": "always",
    "refresh": 5,
    "concurrency": 3
  },
}
```
port: number | "env:variable" | string

Port that the proxy is listening on. Acceptable formats are:

Integer: actual port number.
Environment variable: Beginning with "env:", an env variable specifies the port in string format, such as "env:PORT". V2Ray will decode the variable as string.
String: A numberic string value, such as "1234", or a range of ports, such as "5-10" for 6 ports in total.
The actual ports to open also depend on allocate setting. See below.

listen: address

The address to be listened on. Default value is "0.0.0.0" for incoming connections on all network interfaces. Otherwise the value has to be the address of an existing network interface.

protocol: string

Name of the inbound protocol. See each individual for available values.

settings: InboundConfigurationObject

Protocol-specific settings. See InboundConfigurationObject defined in each protocol.

streamSettings: [StreamSettingsObject]

See Protocol Transport Options for detail.

tag: string

The tag of the inbound proxy. It can be used for routing decisions. If not empty, it must be unique among all inbound proxies.

sniffing: SniffingObject

Configuration for content sniffing.

allocate: AllocateObject

Configuration for port allocation.

SniffingObject
```
{
  "enabled": false,
  "destOverride": ["http", "tls"]
}
```
enabled: true | false

Whether or not to enable content sniffing.

destOverride: ["http" | "tls"]

An array of content type. If the content type of incoming traffic is specified in the list, the destination of the connection will be overwritten by sniffed value.

AllocateObject
```
{
  "strategy": "always",
  "refresh": 5,
  "concurrency": 3
}
```
strategy: "always" | "random"

Strategy of port allocation. When it is set to "always", all port in the port field will be allocated for listening. If "random" is set, V2Ray will listen on number of concurrency ports, and the list of ports are refereshed every refresh minutes.

refresh: number

Number of minutes to refresh the ports of listening. Min value is 2. This setting is only effective when strategy is set to "random".

concurrency: number

Number of ports to listen. Min value is 1. Max value is one third of entire port range.

OutboundObject
An OutboundObject defines an outbound proxy for handling out-going connections. Available protocols are listed here.
```
{
  "sendThrough": "0.0.0.0",
  "protocol": "protocol_name",
  "settings": {},
  "tag": "this_outbound_tag_name",
  "streamSettings": {},
  "proxySettings": {
    "tag": "another_outbound_tag_name"
  },
  "mux": {}
}
```
sendThrough: address

An IP address for sending traffic out. The default value, "0.0.0.0" is for randomly choosing an IP available on the host. Otherwise the value has to be an IP address from existing network interfaces.

protocol: string

The protocol name of this outbound. See Protocols for all available values.

settings: OutboundConfigurationObject

Protocol-specific settings. See OutboundConfigurationObject in each individual protocols.

tag: string

The tag of this outbound. If not empty, it must be unique among all outbounds.

streamSettings: StreamSettingsObject

Low-level transport settings. See Protocol Transport Options.

proxySettings: ProxySettingsObject

Configuration for delegating traffic from this outbound to another. When this is set, streamSettings of this outbound will has no effect.

mux: MuxObject

See Mux configuration for detail.

ProxySettingsObject
```
{
  "tag": "another-outbound-tag"
}
```
tag: string

When tag is set to the tag of another outbound, the out-going traffic of current outbound will be delegated to the specified one.
