# All In One Internet of Things App
[![aio-iot-app](https://snapcraft.io/aio-iot-app/badge.svg)](https://snapcraft.io/aio-iot-app)

[![Get it from the Snap Store](https://snapcraft.io/static/images/badges/en/snap-store-black.svg)](https://snapcraft.io/aio-iot-app)

## Overview
This is a `Rexroth unofficial app` for the ctrlX Automation platform. If you are looking for `offical` apps from Rexroth, have a look at the [ctrlX Store](https://developer.community.boschrexroth.com/t5/Store-and-How-to/bg-p/dcdev_community-dev-blog/label-name/rex_c_Store).

This app is a bundle of `Node RED`, `InfluxDB` and `Grafana` snaps available on Snap Store. The main onjective of this app is to give users a simpler and a faster way to create a monitoring solution.

These are the snaps bundled:
```yaml
  - influxdb-ijohnson/latest/stable
  - node-red/latest/stable
  - on amd64: [grafana/latest/stable]
  - on arm64: [grafana/latest/beta]
```

And these are the flows preinstalled on `Node RED`:
```yaml
  - node-red-contrib-influxdb
  - node-red-contrib-ctrlx-automation
  - node-red-dashboard 
  - node-red-contrib-opcua
```

Snaps are using their default ports:
```yaml
  - Node RED: 1880
  - InfluxDB: 8086
  - Grafana: 3000
```

## Influx Authentication
As of `Release v1.2`, I have enabled the HTTP authentication mecanism of the Influx DB. This means that, right after installing the app, it is required to create an admin user with the all privileges. Refer to [Influx official documentation](https://docs.influxdata.com/influxdb/v1.8/administration/authentication_and_authorization/#user-management-commands) on how to manage users.

User management on Influx has to be done using the Influx CLI. You can do it using on your own PC by setting `-host 'ip_address' -port '8086'` on the CLI, or you can do it directly on the ctrlX Core. However, terminal access trough SSH is disabled by default on ctrlX Core. So because of that, you'll need to use the `exec` node from `Node RED`. I've already set the path so you don't have to. Import the [set_auth.json](https://raw.githubusercontent.com/lg-lima1/aio-iot-app/master/set-auth.json) in your Node RED flow and use Node RED Dashboard on path `<ctrlx_ip_address>:1880/ui/`.

```
# Create admin user
CREATE USER admin WITH PASSWORD '<password>' WITH ALL PRIVILEGES

# Create non-admin user. Privileges can be READ, WRITE or ALL.
CREATE USER <username> WITH PASSWORD '<password>' WITH '[READ, WRITE, ALL]' PRIVILEGES
```

If you try to insert a value to the `db` database before configuring users, the following error will pop up.
```
Error: A 404 Not Found error occurred: {"error":"database not found: \"db\""}
```

And if you do not set the user correctly, the following error will pop up. Keep in mind that after creating the user, you need to update the influx node credentials inside Node RED.
```
ERR: error authorizing query: create admin user first or disable authentication
Warning: It is possible this error is due to not setting a database.Please set a database with the command "use <database>".
```

## ctrlX Core 1V12 or greater
Please note that on recent versions of the ctrlX Core, `Ubuntu Core` has been updated to the version 20 LTS. This means that dependency `core18`, required from the `node-red` official snap, does not come out of the box. In order to fix this, you have to install the `core18` snap manually as a system package. Not doing so will result in an error during the installation.
