# Heroku Backup Page 
When shopify goes down, we update DNS to point www.loandsons.com to Heroku instead of Shopify. These updates should only be done when Shopify has been down for 15 minutes or longer. 

[Watch the Shopify status page for service restoration updates](https://status.shopify.com/)

DNS is managed by Cloudflare:

## Site Down Steps
1. Update the `www` CNAME target to `developmental-damselfly-b27748byfaumgi0soylfmzfl.herokudns.com`. This maps to a custom domain configured in Heroku.
2. Delete the A record for `loandsons.com`. Do this before step 3 to avoid throwing a conflicting name error with the root (`@`) CNAME record we're adding next.
3. Add a CNAME record `@` with target `thermal-sawfish-q5owfe95j1ojbky8wubrijgl.herokudns.com`. This also maps to a Heroku custom domain.

## Site Up Steps
1. Update the `www` CNAME record target to `shops.myshopify.com`. This is the Primary domain in Shopify.
2. Delete the `loandsons` CNAME record. This allows us to create A record next.
3. Add an A record for `loandsons.com` pointing to `23.227.38.32`. This is a Third-party domain in Shopify and will redirect to the www version.


## After DNS Updates
1. In the store admin > Online Store > Domains, double check that the Primary domain and Third-part domains are showing as **disconnected** for site down and **connected** for site up.
 2. When **disconnected**, both _www.loandsons.com_ and _loandsons.com_ should show the Heroku page. 
 3. When **connected** we're back at Shopify again.
4. If the site isn't resolving to Heroku, double check the [app settings](https://dashboard.heroku.com/apps/loandsons/settings) > Domains in case the DNS target has changed from what's documented here (unlikely).  

## About CNAME Flattening
Using `@` in a CNAME record causes cloudflare to "[flatten](https://support.cloudflare.com/hc/en-us/articles/200169056-Does-CloudFlare-support-CNAME-APEX-at-the-root-)" the request to an A record. Since Heroku apps are ephemeral, [we can't just point the existing A record to a Heroku IP](https://devcenter.heroku.com/articles/custom-domains#add-a-custom-root-domain) when Shopify goes down. Instead, we must use a root CNAME to achieve the same result. 