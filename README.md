# AWS EC2 Instances
<img src="https://d1xu7knqe2z7f9.cloudfront.net/appnroll/appnroll-192x192.png" align="right"
     title="Size Limit logo by Anton Lovchikov" width="80" height="80">
     
`aws_ec2_instances_from_all_regions_to_db.zsh`

This script lists all AWS instances in all regions and saves to PostgreSQL database. 
Fast and neat way to audit you AWS EC2 instances!
Tested that works not only on `zsh` but also on `sh`.

![](https://d1xu7knqe2z7f9.cloudfront.net/appnroll/all-ec2-instances.gif)

this script was an experiment to save data from AWS-CLI using pure bash scripts. 
I liked the idea that no ruby or node backend is needed, so the dependencies are limited to:

`aws-cli` - Universal Command Line Interface for Amazon Web Services https://github.com/aws/aws-cli

`jq` - a Command-line JSON processor https://github.com/stedolan/jq

`psql` - Command Line tool that comes together with `postgresql`

`createdb` - Command Line tool that also comes together with `postgresql` :-)

which you usually have installed anyway on your local environment.

### Prerequisites

* We need at least PostgreSQL 9.5 as it supports `upsert` that is update on conflict. Commands `createdb` and `psql` come with it (not tested on Ubuntu, maybe it needs extension here).

to install postgreSQL on Mac

```shell
brew install postgresql
```

* The script assumes you have configured the AWS CLI
https://aws.amazon.com/cli/


```shell
brew install awscli
```


* For the script to work you need have `jq` installed as we need the json output of aws-cli to be parsed


to install jq on Mac

```shell
brew install jq

```
### Usage

0) make sure you have the preconditions above, then

* clone the repo:

```shell
git clone https://github.com/Appnroll/aws-ec2-instances.git
```
* enter the repo:

```shell
cd aws-ec2-instances
```

1) first create the database:
```shell
createdb aws_instances
```

2) then create the table from the script in sql folder:
```shell
psql aws_instances -f sql/aws_ec2_instances.sql
```

before you need to run it to create `aws_instances` database in postgres
with table `aws_ec2` and fields as in the variable $SAVED_FIELDS.
The naming of course you can change directly in `sql/aws_ec2_instances.sql`

3) then run the script:
```shell
zsh aws_ec2_instances_from_all_regions_to_db.zsh $DATABASE $TABLE_NAME
```

4) Bonus step! If all works fine you can repeat with multiple environments.

expect it to open a csv file with all the instances recorded to the database
with the default program.

```shell
zsh ec2_instances_in_multiple_profiles.zsh
```
you can rerun this script for all future for updates


## Troubleshooting

you may get `Failed to parse JSON, or got false/null`
This means you probably don't have `jq` installed.


## Multiple profiles

Here is a script if you need to fetch the data from
multiple profiles. Before you run it:
Just pass the names of your AWS profiles you can find in
`~/.aws/config`

to the `profiles` variable bash array in the 
`ec2_instances_in_multiple_profiles.zsh` in line 4.
Careful - no commas in the array! It should look like this:
```shell
profiles=("default" "appnroll")
```

then you can run

```shell
zsh ec2_instances_in_multiple_profiles.zsh
```

You can have an example config like this:

#### example ~/.aws/config
```
[profile profile_1]
region=us-east-2
output=json

[profile profile_2]
region=us-east-1
output=json
```

#### example ~/.aws/credentials
```
[profile_1]
aws_access_key_id = YOUR_PROFILE_1_KEY_HERE
aws_secret_access_key = YOUR_PROFILE_1_SECRET_ACCESS_KEY_HERE

[profile_2]
aws_access_key_id = YOUR_PROFILE_2_KEY_HERE
aws_secret_access_key = YOUR_PROFILE_2_SECRET_ACCESS_KEY_HERE
```


## RDS instances

There is an analogical script for RDS instances.
If you have sufficient privileges run:

```
zsh aws_rds_instances_from_all_regions_to_db.zsh
```

and for multiple profiles:
```
zsh rds_instances_in_multiple_profiles.zsh
```


## Contributing

You are welcome to add issues and create pull requests.
The script may need some extensions for sure, that is different databases, only terminal output, 
html, csv and maybe also a version that supports connection strings. 
That's a short wishlist!

## License

The code in this project is licensed under MIT license.

## Links

* https://starkandwayne.com/blog/bash-for-loop-over-json-array-using-jq/
* https://stedolan.github.io/jq/tutorial/
* https://medium.com/cameron-nokes/working-with-json-in-bash-using-jq-13d76d307c4
* https://www.codementor.io/engineerapart/getting-started-with-postgresql-on-mac-osx-are8jcopb
* https://stackoverflow.com/questions/46695013/how-can-i-use-update-with-json-populate-record-in-postgresql
