
.. meta::
   :description: A sample NGINX configuration for osCommerce.

osCommerce
==========

Recipe
------

osCommerce example configuration for 2.3.4 (maybe earlier too)

.. code-block:: nginx

    server {
        server_name www.domain.com;
        server_name_in_redirect off;

        root /var/www/www.domain.com/catalog;
        access_log /var/www/www.domain.com/logs/access.log;
        error_log /var/www/www.domain.com/logs/error.log;

        # expires max on static content
        location / { expires max; }

        # Inaccessible locations
        location ~ ^/includes/.*\.php$ { return 403; }
        location ~ ^/admin/includes/.*\.php$ { return 403; }
        location ^~ /admin/backups { return 403; }
        location ^~ /download { return 403; }

        # osCommerce rewrites
        location ~ ^(.*)-p-(?<id>[0-9]+).html$ { rewrite ^ /product_info.php?products_id=$id&$args; }
        location ~ ^(.*)-c-(?<id>[0-9]+).html$ { rewrite ^ /index.php?cPath=$id&$args; }
        location ~ ^(.*)-m-(?<id>[0-9]+).html$ { rewrite ^ /index.php?manufacturers_id=$id&$args; }
        location ~ ^(.*)-pi-(?<id>[0-9]+).html$ { rewrite ^ /popup_image.php?pID=$id&$args; }
        location ~ ^(.*)-pr-(?<id>[0-9]+).html$ { rewrite ^ /product_reviews.php?products_id=$id&$args; }
        location ~ ^(.*)-pri-(?<id>[0-9]+).html$ { rewrite ^ /product_reviews_info.php?products_id=$id&$args; }
        location ~ ^(.*)-by-(?<id>[0-9]+).html$ { rewrite ^ /all-products.php?fl=$id&$args; }
        
        # Articles contribution
        location ~ ^(.*)-t-(?<id>[0-9]+).html$ { rewrite ^ /articles.php?tPath=$id&$args; }
        location ~ ^(.*)-a-(?<id>[0-9]+).html$ { rewrite ^ /article_info.php?articles_id=$id&$args; }
        location ~ ^(.*)-au-(?<id>[0-9]+).html$ { rewrite ^ /articles.php?authors_id=$id&$args; }
        
        # Information pages
        location ~ ^(.*)-i-(?<id>[0-9]+).html$ { rewrite ^ /information.php?info_id=$id&$args; }
        location ~ ^(.*)-pm-([0-9]+).html$ { rewrite ^ /info_pages.php?pages_id=$id&$args; }
        
        # Links contribution
        location ~ ^(.*)-links-(?<id>[0-9]+).html$ { rewrite ^ /links.php?lPath=$id&$args; }
        
        # Newsdesk contribution
        location ~ ^(.*)-n-(?<id>[0-9]+).html$ { rewrite ^ /newsdesk_info.php?newsdesk_id=$id&$args; }
        location ~ ^(.*)-nc-(?<id>[0-9]+).html$ { rewrite ^ /newsdesk_index.php?newsPath=$id&$args; }
        location ~ ^(.*)-nri-(?<id>[0-9]+).html$ { rewrite ^ /newsdesk_reviews_info.php?newsdesk_id=$id&$args; }
        location ~ ^(.*)-nra-(?<id>[0-9]+).html$ { rewrite ^ /newsdesk_reviews_article.php?newsdesk_id=$id&$args; }
        
        # Others
        location ~ ^(.*)-f-(?<id>[0-9]+).html$ { rewrite ^ /faqdesk_info.php?faqdesk_id=$id&$args; }
        location ~ ^(.*)-fc-(?<id>[0-9]+).html$ { rewrite ^ /faqdesk_index.php?faqPath=$id&$args; }
        location ~ ^(.*)-fri-(?<id>[0-9]+).html$ { rewrite ^ /faqdesk_reviews_info.php?faqdesk_id=$id&$args; }
        location ~ ^(.*)-fra-(?<id>[0-9]+).html$ { rewrite ^ /faqdesk_reviews_article.php?faqdesk_id=$id&$args; }
        location ~ ^(.*)-po-(?<id>[0-9]+).html$ { rewrite ^ /pollbooth.php?pollid=$id&$args; }
        # Pass to php
        location ~ \.php$ {
            if (!-f $request_filename) { return 404; }
            include fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            fastcgi_param REDIRECT_STATUS 200;
            fastcgi_pass 127.0.0.1:9000;
        }
    }

