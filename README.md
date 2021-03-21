# certbot-in-docker

+ ## [What’s Certbot?](https://certbot.eff.org/about)

+ ## What does this YML do?
  If you have browsed the [docker-compose.yml](docker-compose.yml), you would find it just modified the entrypoint directive!
  I doubt the official entrypoint directive runs certbot command directly, sometimes not a good idea, so I changed it.

+ ## How to use?
  Run in terminal:
  ```shell
  sudo docker exec -it certbot sh
  ```
  now you are in the docker container, feel free to use certbot!

+ ## Example
  ### Assume that your HTTP website is running Nginx on Debian(buster), and you want a wildcard certificate.
  
  ***There are generally two ways to generate a certificate, one which we recommend is using the DNS plugin,
    and the other one is using the manual plugin, which we don't recommend,
    because you need to manually renew the certificate, which will be a troublesome thing!***

    + ### DNS plugin(recommend)
      If your DNS provider is Cloudflare, you'd run the following command:
      ```shell
      certbot certonly \
        --server https://acme-v02.api.letsencrypt.org/directory \
        --email youremail \
        --dns-cloudflare \
        --dns-cloudflare-credentials ~/.secrets/certbot/cloudflare.ini \
        --dns-cloudflare-propagation-seconds 60 \
        -d 'domain.com, *.domain.com'
      ```
      For more details or comprehensive usage, you should refer to the
        [official](https://certbot.eff.org/lets-encrypt/debianbuster-nginx).

    + ### manual plugin(not recommend)
      If you decide to use the manual mode, it's presumably better to use the docker
        [image](https://hub.docker.com/r/certbot/certbot) without any plugins. Run the following command:
      ```shell
      certbot certonly \
        --server https://acme-v02.api.letsencrypt.org/directory \
        --email youremail \
        --manual \
        --preferred-challenges dns \
        -d 'domain.com, *.domain.com'
      ```
      Some interactive content would appear on terminal,
        you need to go to your DNS provider to add certain type of records manually for your domain name.  
      For more details or comprehensive usage, you should refer to the
        [official](https://certbot.eff.org/docs/using.html#manual).

+ ## Renew
    + ### DNS plugin
      In this mode, you will not need to run Certbot again, unless you change your configuration.
      You can test automatic renewal for your certificates by running this command:
      ```shell
      certbot renew --dry-run
      ```
      The command to renew certbot is installed in one of the following locations:
      ```text
      /etc/crontab/
      /etc/cron.*/*
      systemctl list-timers
      ```

    + ### manual plugin
      In this mode, you need to run the corresponding command again!
      You need to add some records manually, which can be very cumbersome!

+ ## Confirm that Certbot worked
  To confirm that your site is set up properly,
    visit `https://yourwebsite.com` in your browser and look for the lock icon in the URL bar.
  