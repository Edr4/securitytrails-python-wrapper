# securitytrails-python-wrapper

```py
import argparse
import json
import requests
import subprocess

parser = argparse.ArgumentParser(description='Query the SecurityTrails API for subdomains')
parser.add_argument('-d', '--domain', required=True, help='The domain to query')
args = parser.parse_args()

url = f'https://api.securitytrails.com/v1/domain/{args.domain}/subdomains'
headers = {'apikey': '{{API_KEY}}'}

response = requests.get(url, headers=headers)

result = []

def dig_check_a_domain(domain):
    try:
        output = subprocess.check_output(['dig', '+short', domain])
        output = output.decode('utf-8').strip().split('\n')
        if output:
            result.append(domain)
    except subprocess.CalledProcessError:
        print(f"Error occurred while checking {domain}.")

if response.ok:
    data = json.loads(response.text)
    subdomains = data['subdomains']
    for subdomain in subdomains:
        domain = subdomain + "." + args.domain
        dig_check_a_domain(domain)
else:
    print(f'Request failed with status {response.status_code}: {response.text}')

for domain in result:
    print(domain)
```
