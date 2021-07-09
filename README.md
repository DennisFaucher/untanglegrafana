# Untangle PostgreSQL Grafana Dashboards
![Screen Shot 2021-07-09 at 2 49 01 PM](https://user-images.githubusercontent.com/9034190/125123591-d794d800-e0c4-11eb-9645-17d2022ec1f1.png)

## Why
I just installed an Untangle firewall appliance in my home network. I like it a lot. Great functionality, easy to use, great reports, very low resource usage. As a typical Grafana user, I needed to GRAPH ALL THE THINGS! Untangle is open source and even uses a standard PostgreSQL database. Grafana supports PostgreSQL so connecting the two should be easy. Well, nothing is ever easy is it? 😃

## How
### Parts List
* Grafana Up and Running ([Guide](http://blog.faucher.net/2021/02/grafana-101-part-i.html))
* Untangle Up and Running ([Guide](https://www.untangle.com/untangle-ng-firewall/resources/how-to-deploy/))

### Open Up Untangle PostgreSQL to Other Hosts
My Grafana server is separate from my Untangle appliance naturally. Although I could access PostgreSQL my ssh-ing into the Untangle appliance and typing "psql -U postgres",  typing "psql -U postgres -h untangle" got me nowhere. After some research, I learned that PostgreSQL is only acessible by localhost be default. Smart. One needs to edit two PostgreSQL files to allow access from other hosts - postgresql.conf and pg_hba.conf. 

````bash
/etc/postgresql/11/main/postgresql.conf
#listen_addresses = 'localhost'         # what IP address(es) to listen on;
listen_addresses = '*'          # what IP address(es) to listen on;
                                        # comma-separated list of addresses;
                                        # defaults to 'localhost'; use '*' for all
                                        # (change requires restart)

/etc/postgresql/11/main/pg_hba.conf
# IPv4 local connections:
host    all             all             127.0.0.1/32            trust
host    all             all             192.168.1.0/24            trust
````

## Thank You
