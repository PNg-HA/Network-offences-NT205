# RCE Wordpress plugin with CVE-2022-1329

## Vulnerable plugins: Elementor 3.6.0 → 3.6.2

### Link download: [Elementor Website Builder – More than Just a Page Builder (advanced view) – WordPress plugin | WordPress.org](https://wordpress.org/plugins/elementor/advanced/)

![Untitled](RCE%20Wordpress%20plugin%20with%20CVE-2022-1329%20159bc42a583349619ec9ed229b4febe5/Untitled.png)

In this work, I download version 3.6.1

## Reference:

1. https://github.com/Grazee/CVE-2022-1329-WordPress-Elementor-RCE
2. [(0x02) Creating Exploits - Wordpress Elementor 3.6.0, 3.6.1, 3.6.2 (RCE) CVE-2022-1329 [ENG] - YouTube](https://www.youtube.com/watch?v=tIhN1svzAYk&ab_channel=AkuCyberSec)
3. [Exploit-DB - WordPress Plugin Elementor 3.6.2 - Remote Code Execution (RCE) (Authenticated) - YouTube](https://www.youtube.com/watch?v=LNhzjGdt-_4&ab_channel=BHack)

Mostly of this work is from the first link. The second is for the elementor-pro.zip file. The last is for fixing bugs. **Remember the order.**

## CVE summary (from my perspective):

The plugin has not checked the PHP upload file from an authenticated user (not necessary Admin). That’s all for a hacker to add a malformed script to exploit the web server.

## Usage:

cd to the folder that stores docker-compose.yaml

`docker compose up`

Then you can access the WordPress Admin page with URL `http://localhost:8000`.

Register the admin user, and add a **no-admin** user in the **Users** tag. For example, there are two users:

| Username | Password | Role |
| --- | --- | --- |
| wc | 123456 | Admin |
| a2 | aaaaaa | Editor |

Log in with the admin account.

Add **php.ini** with following content to `/var/www/html/`: 

    upload_max_filesize = 64M
    max_execution_time = 0
    post_max_size = 150M
    file_uploads = on

`docker cp php.ini <docker-wordpress-ID>:/var/www/html`
Then restart the wordpress container with `docker restart`.

Install [elementor.3.6.1.zip](https://github.com/Grazee/CVE-2022-1329-WordPress-Elementor-RCE/blob/main/elementor.3.6.1.zip) to Wordpress and activate.

In poc.py, modify these variables:

![Untitled](RCE%20Wordpress%20plugin%20with%20CVE-2022-1329%20159bc42a583349619ec9ed229b4febe5/Untitled%201.png)

Remember:

- baseUrl has ‘http’; the url can be dns+public.
- zip file: Not the zip in the first link. (about 2KB)
    - a folder named elementor-pro,
        - the folder has the file elementor-pro.php (2nd link, turn off security)

Then: `python .\poc.py` (for Windows). If prints out:

![Untitled](RCE%20Wordpress%20plugin%20with%20CVE-2022-1329%20159bc42a583349619ec9ed229b4febe5/Untitled%202.png)

Then you are succussed. Reload web with user that you use in poc.py

![Untitled](RCE%20Wordpress%20plugin%20with%20CVE-2022-1329%20159bc42a583349619ec9ed229b4febe5/Untitled%203.png)
