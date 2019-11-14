# loandsons-heroku
When shopify goes down, we update DNS to point loandsons.com to Heroku instead of Shopify. This should only take place when Shopify has been down for 15 minutes or longer. 

[Shopify status page](https://status.shopify.com/)

DNS is managed by cloudflare, so login there and go to the DNS page to make the following changes:

## Site Down Steps
1. Rename the `sitedown` CNAME record to `@`
2. Delete the A record for `loandsons.com`

## Site Up Steps
1. Rename the `@` CNAME record to `sitedown`
2. Add an A record for `loandsons.com` pointing to `23.227.38.32`

## CNAME Flattening
Using `@` in a CNAME record causes cloudflare to "[flatten](https://support.cloudflare.com/hc/en-us/articles/200169056-Does-CloudFlare-support-CNAME-APEX-at-the-root-)" the request to an A record. Since heroku apps are ephemeral, [we can't just point the existing A record to a heroku IP](https://devcenter.heroku.com/articles/custom-domains#add-a-custom-root-domain). Instead, we must use a root CNAME to achieve the same result. 