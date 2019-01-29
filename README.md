# Jenkins Pipeline for DAST using Owasp Zap and Posting Data to ELK

## Requirements
- Jenkins
- Slave Linux with Docker or Master with Docker
- ELK up and running

## Usage

There are 3 variables that need to be defined in Jenkins Job to use this Jenkinsfile

```
APP_URL - URL that Jenkins Agent will use to access app.
APP_NAME - Name for better use in Kibana
ELASTIC_ADDRESS - URL to Elasticsearch API, used to post data.
```

## Tips

You can import the kibana_dashboard_example.json in your Kibana to use predefined Dashboards e Visualizations.  
The index to be used is bookmark*

Based on https://github.com/boranx/docker-zap-elk