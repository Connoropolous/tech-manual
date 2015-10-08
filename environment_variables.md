# Environment Variables

### Domain name settings
CANONICAL_HOST - Hostname of the loomio instance. For us it's "www.loomio.org"  
TLD_LENGTH - length of the top level part of your domain name.  
DEFAULT_SUBDOMAIN - we use www  
ALLOW_ROBOTS - Set to 1 if you want to search engines to crawl the public discussions and groups.  

Examples:  
  www.loomio.org  
  CANONICAL_HOST = www.loomio.org  
  TLD_LENGTH = 1  
  DEFAULT_SUBDOMAIN = www  

  loomio.somewhereelse.com  
  CANONICAL_HOST = loomio.somewhereelse.com  
  TLD_LENGTH = 2  
  DEFAULT_SUBDOMAIN should not be set  

SECRET_COOKIE_TOKEN -  run 'rake secret' to generate your own SECRET_COOKIE_TOKEN  
DEVISE_SECRET - run 'rake secret' to generate your own DEVISE_SECRET  
FORCE_SSL - if true, only HTTPS connections will be permitted  
FAYE_URL - the url for your FAYE instance - see https://github.com/loomio/private_pub  
MAX_THREADS - optional puma configuration  
MIN_THREADS - optional puma configuration  
PUMA_WORKERS - optional puma configuration  

### Email configuration

SMTP_DOMAIN  
SMTP_PASSWORD  
SMTP_PORT  
SMTP_SERVER  
SMTP_USERNAME  
REPLY_HOSTNAME - we use reply.loomio.org. This is the hostname of your reply by email server.


### Amazon AWS for attachments
AWS_ACCESS_KEY_ID  
AWS_SECRET_ACCESS_KEY  
AWS_BUCKET  

### Social media
FACEBOOK_KEY  
FACEBOOK_SECRET  
TWITTER_KEY  
TWITTER_SECRET  
FB_APP_ID_META  
GOOGLE_KEY  
GOOGLE_SECRET  
OMNI_CONTACTS_GOOGLE_KEY  
OMNI_CONTACTS_GOOGLE_SECRET  

### Analytics
HEAP_APP_ID  
NEW_RELIC_APP_NAME  
TAG_MANAGER_ID  

### Translation related
BING_TRANSLATE_APPID  
BING_TRANSLATE_SECRET  

### Errbit/Airbrake
ERRBIT_KEY  
ERRBIT_HOST  
ERRBIT_PORT  
