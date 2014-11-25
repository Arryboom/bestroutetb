# Best Route Table

Inspired by [chnroutes][chnroutes].

This project aimed to generate the smallest route table,
while preserves the minimalist requirements that IPs of
specified countries or subnets will be routed to a
specified gateway (default or VPN).

Generally speaking, the generated route table is at least
70% smaller than chnroutes's.

查看[使用说明][wiki]


## Objective

I started this project as a result of the huge route table
generated by *chnroutes* didn't fit into my router.

The route table took almost 4 minutes to load up, and it cannot be
put into OpenVPN's configuration file, due to my service
provider pushed `ping-reset 60` to the client, reseted
OpenVPN before route table being loaded up.

Therefore I decided to minimize the route table.


## How efficient it is?

For an example, a route table that route all IPs in China to
default gateway, and US, GB, Japan, Hong kong administered
IPs to VPN gateway (based on 11/21/2014 data,)
only need 1546 routing directives, while *chnroutes* needs
4953 routing directives.

Which is almost **70% smaller**. And if route US address to VPN only,
the route table has only **105 directives**, which is about only
2% of original size.

On Linux system, which usese [TRASH][trash] structure to store
route table, a route lookup operation expected to access
memory O(loglog _n_) times. Using *bestroutetb* instead of *chnroutes*,
will reduce route table look up at least 0.01 times expectedly.
However, this solution does reduce the route table size for 70% by
assuming TRASH structure is implemented with a small overhead
hash implementation.  It is therefore that a perfect approach for those
routers with low free memory.


## How it works

Unlike *chnroutes* which will generate a route table that
route all subnets of China to ISP gateway, while other VPN gateway.
This project divides IPs in three groups. First group is guaranteed
to be routed to default gateway, Second group is guaranteed to be
routed to VPN gateway. And the last group will be dynamically assigned
to one of the gateways, in a manner that will generate
the smallest route table.

To achieve this goal, this project using dynamic programming
algorithm to find the most optimized route table.

We can prove that, the generated route table is the smallest
one based on the given restrictions.

[For further detail][blog].


## How to use

### Install

This project requires [node.js][nodejs] to run.

If you are using OS X install it with homebrew

    $ brew install nodejs

From NPM for use as a command line app:

    $ npm install -g bestroutetb

From NPM for programmatic use:

    $ npm install bestroutetb

From Git

    $ git clone https://github.com/ashi009/bestroutetb.git
    $ cd bestroutetb
    $ npm link .

### Usage

    $ bestroutetb [options]

### Options

#### Route

    --route.net=<spec>
    --route.vpn=<spec>

Subnets that should be routed to ISP gateway
Subnets that should be routed to VPN gateway

#### Profile

    -p <profile>, --profile=<profile>

Output format profile: `custom`, `iproute`, `json`, `openvpn`

#### Output

    -o <path>, --output=<path>

Output file path

    -f, --force

Force to overwrite existing files

#### Report

    -r <path>, --report=<path>

Generate a report and save to given path

#### Output format

    --header=<string>
    --footer=<string>

Header and Footer of the output file

    --rule-format=<format>

String used to format a rule when `--profile=custom`

    --gateway.net=<string>
    --gateway.vpn=<string>

Substitute for `%gw` when rule is using ISP gateway
Substitute for `%gw` when rule is using VPN gateway

    --[no-]default-gateway

Output directive for default route (0.0.0.0/0)

    --[no-]group-gateway

Group rules by gateway

    --group-header=<string>
    --group-footer=<string>

Header and of each group

    --group-name.net=<string>
    --group-name.vpn=<string>

Substitute for `%name` when group is route to ISP gateway

#### Update

    --[no-]update

Force update delegation data, or force to use stale data

#### Config

    -c <path>, --config=<path>

Configuration file path

#### Logging

    -v, --verbose

Verbose output

    -s, --silent

Silent mode

#### Help

    -h, --help

Show help

#### Version

    -V, --version

Show version number

### Examples

    $ bestroutetb --route.vpn=us -p json -o routes.json

[chnroutes]: https://github.com/fivesheep/chnroutes
[wiki]: https://github.com/ashi009/bestroutetb/wiki/%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E
[trash]: http://www.nada.kth.se/~snilsson/publications/TRASH/trash.pdf
[blog]: http://ashi009.tumblr.com/post/36581070478/vpn
[nodejs]: http://nodejs.org
[wget]: http://www.gnu.org/software/wget/
