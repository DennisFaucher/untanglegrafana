# Untangle PostgreSQL Grafana Dashboards

![Screen Shot 2021-07-09 at 6 24 28 PM](https://user-images.githubusercontent.com/9034190/125141381-eee2be00-e0e2-11eb-8385-c06f5952f9e3.png)

## Why
I just installed an Untangle firewall appliance in my home network. I like it a lot. Great functionality, easy to use, great reports, very low resource usage. As a typical Grafana user, I needed to GRAPH ALL THE THINGS! Untangle is open source and even uses a standard PostgreSQL database. Grafana supports PostgreSQL so connecting the two should be easy. Well, nothing is ever easy is it? 😃

## How
### Parts List
* Grafana Up and Running ([Guide](http://blog.faucher.net/2021/02/grafana-101-part-i.html))
* Untangle Up and Running ([Guide](https://www.untangle.com/untangle-ng-firewall/resources/how-to-deploy/))

### Open Up Untangle Firewall to Allow Internal PostgreSQL Access
![Screen Shot 2021-07-09 at 5 36 49 PM](https://user-images.githubusercontent.com/9034190/125138248-4cbfd780-e0dc-11eb-851c-5a8f4615d537.png)

Untangle, being an exellent security appliance, is locked down tight.  The only access to the appliance are very tightly controlled SSH, HTTPS, RADIUS, DNS, DHCP and a few other internal-only ports. We want to allow access to the PostgreSQL port (5432) from the internal network (Grafana). 

In the Untangle web interface go to Config > Network > Advanced > Access Rules. Add this rule:

![Screen Shot 2021-07-09 at 5 43 57 PM](https://user-images.githubusercontent.com/9034190/125138779-454cfe00-e0dd-11eb-83d7-d669e3da9fa8.png)

Move the rule up to the top and click Save.

### Open Up Untangle PostgreSQL Database to Other Hosts
My Grafana server is separate from my Untangle appliance naturally. Although I could access PostgreSQL by ssh-ing into the Untangle appliance and typing "psql -U postgres",  typing "psql -U postgres -h untangle" got me nowhere. After some research, I learned that PostgreSQL is only acessible by localhost by default. Smart. One needs to edit two PostgreSQL files to allow access from other hosts - postgresql.conf and pg_hba.conf. In postgresql.conf change 'localhost' to '*' and in pg_bha_conf add the a line that points to your internal network like "host all all 192.168.1.0/24 trust". I found [this guide](https://www.bigbinary.com/blog/configure-postgresql-to-allow-remote-connection) helpful.

````bash
/etc/postgresql/11/main/postgresql.conf
#listen_addresses = 'localhost'         # what IP address(es) to listen on;
listen_addresses = '*'                  # what IP address(es) to listen on;
                                        # comma-separated list of addresses;
                                        # defaults to 'localhost'; use '*' for all
                                        # (change requires restart)

/etc/postgresql/11/main/pg_hba.conf
# IPv4 local connections:
host    all             all             127.0.0.1/32            trust
host    all             all             192.168.1.0/24            trust
````

After editing these two files run the command
````bash
systemctl restart postgresql
````
to enable your changes. Now running the command
````bash
psql -U postgres -h untangle
````
should work (as long as you use the correct hostname/IP address after -h).

### Add Untangle PostgreSQL as a Grafana Data Source

![Screen Shot 2021-07-09 at 6 23 16 PM](https://user-images.githubusercontent.com/9034190/125141344-c6f35a80-e0e2-11eb-9712-f31f38fb43ae.png)


## Thank You
