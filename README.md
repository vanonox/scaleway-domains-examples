# scaleway-domains-examples
examples to use Scaleway Domains API

the examples are for your testing environment

## Requirements

### Generate your API TOKEN

Login to your Scaleway account and create a token from the `credentials`
page. A `secret_key` and an `access_key` will be displayed on your screen, the
`secret_key` will be used as `X-Auth-Token`.

Reference: https://www.scaleway.com/docs/generate-an-api-token/

### Get your organization id

The organization id is required to get the permissions. You will need to query:

```bash
curl https://account.scaleway.com/organizations -H "X-Auth-Token: $secret_key"
```

To get your organization id:

```json
{
   "organizations":[
      {
         "id":"000a115d-2852-4b0a-9ce8-47f1134ba95a", <-----
         "name":"jsnow@got.wint",
         "users":[
            {
               ...
            }
         ]
      }
   ]
}
```

Reference: https://www.scaleway.com/docs/retrieve-my-organization-id-throught-the-api/

## Rest API

please read https://developers.scaleway.com/en/products/domain/api/ before using

### easiest way to update a single record with a single value:

domain: domain-test.fr
zone: domain-test.fr

```
curl --request PATCH \
  --url https://api.scaleway.com/domain/v2alpha2/dns-zones/domain-test.fr/records \
  --header 'content-type: application/json' \
  --header 'x-auth-token: xxxxxxxxx' \
  --data '{
	"return_all_records": false,
  "changes": [
		{
			"set": {
						"name": "test",
						"type": "A",				
				"records": [
					{
						"name": "test",
						"data": "1.2.3.4",
						"type": "A",
						"ttl": 3600
					}
				]
			}
		}
  ]
}'
```

### list your records

```
curl --request GET \
--url 'https://api.scaleway.com/domain/v2alpha2/dns-zones/doamin-test.fr/records/' \
--header 'x-auth-token: XXXXXXXXX'
```

## Ansible Module

use the repo https://github.com/vanonox/ansible-scaleway

* there is some playbooks to update the DNS
* playbooks to manage your Domains will be added in the future

## Certbot for LE certs

use the branch https://github.com/vanonox/certbot/tree/scaleway

* clone the git and go to certbot-dns-scaleway 
* make a local directory to store : `mkdir certs`
* copy the scaleway.ini in certbot-dns-scaleway to `certs` and edit scaleway.ini to insert your access token
* build a local image :  `docker build . -t certbot-test`
* execute the certbot to create a new certificate ,
for example :
`docker run -it --volume `pwd`/certs:/etc/letsencrypt certbot-test certonly -a certbot-dns-scaleway:dns-scaleway --certbot-dns-scaleway:dns-scaleway-credentials /etc/letsencrypt/scaleway.ini --agree-tos --noninteractive --email email@email.fr -d www.domain.test`
