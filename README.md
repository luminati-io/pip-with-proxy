# Using pip with Proxies

[![Promo](https://github.com/luminati-io/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.com/) 

This guide explains how to set up proxies with pip to bypass restrictions, improve security, and streamline package management:

- [Public vs Private Proxies](#public-vs-private-proxies)
- [Using Proxies with pip](#using-proxies-with-pip)
- [Configuring a pip Proxy with the Command Line](#configuring-a-pip-proxy-with-the-command-line)
- [Configuring a pip Proxy with the pip Config File](#configuring-a-pip-proxy-with-the-pip-config-file)
- [Configuring a pip Proxy with Environment Variables](#configuring-a-pip-proxy-with-environment-variables)
- [Testing the Configuration](#testing-the-configuration)
- [Troubleshooting pip Proxies](#troubleshooting-pip-proxies)
- [Using pip with Rotating Proxies](#using-pip-with-rotating-proxies)
- [Benefits of Using Proxies with pip](#benefits-of-using-proxies-with-pip)
- [Common Mistakes and Best Practices](#common-mistakes-and-best-practices)
- [Using Bright Data Proxies](#using-bright-data-proxies)
- [Conclusion](#conclusion)

## Public vs Private Proxies

### Public Proxies

Public proxies are accessible to anyone and typically do not require authentication. While they offer a quick way to use an alternative IP address, they have notable drawbacks, including slower speeds, unstable connections, and a higher risk of IP bans. Their free and widespread availability often means they lack key features such as proxy rotation, caching, and access control, making them unsuitable for reliable use in a production environment.

A public URL may be formatted like this: `https://proxyserver:port`.

### Private Proxies

Private proxies require authentication, providing enhanced security, stability, and advanced features, though they often come at a price. They deliver a fast, reliable connection to a dedicated IP address and include functionalities such as proxy authentication and rotation for improved performance and control.

Access is usually controlled through authentication often by including a username and password as a prefix to the proxy URL like this: `https://username:password@proxyserver:port`.

## Using Proxies with pip

Before using a proxy with pip, you need to collect relevant details about your proxy. The following example demonstrates how to use a public proxy with these settings:

- A **proxy address** for the proxy service
- The **port** the proxy service requires for communication

The [following `proxy-list` repo](https://github.com/clarketm/proxy-list) provides daily tested public proxy addresses that can be useful for testing but should not be used in production environments.

Within the `proxy-list` repo, check the [`proxy-list-status.txt`](https://github.com/clarketm/proxy-list/blob/master/proxy-list-status.txt) file to find a working public proxy. This can be done by checking the file for an address that has the `success` flag next to it, indicating that it is working:

![Selecting a public proxy](https://brightdata.com/wp-content/uploads/2025/02/Selecting-a-public-proxy-1024x786.png)

For this tutorial, use `45.185.162.203:999` as your public proxy address. This means the proxy server address is `http://45.185.162.203:999`.

## Configuring a pip Proxy with the Command Line

The quickest way you can configure a pip proxy is to pass in the address when calling the `pip install` command using the `--proxy` command line option.

To verify access through the public proxy and test package retrieval, run the following command:

```
# Public Proxy
pip install boto3 --proxy http://45.185.162.203:999
```

This approach is useful for quickly testing and validating proxies before configuring them permanently. For those publishing pip packages, it also helps confirm availability from a different IP address.

## Configuring a pip Proxy with the pip Config File

To permanently configure a pip proxy, the pip config file is an easy and declarative solution. Its location depends on your operating system and can be found in the following directories:

- **Global:** System-wide configuration file, shared across users.
- **User:** Per-user configuration file for the user executing the pip process.
- **Site:** Per-environment configuration file using Python virtual environments.

These configuration files can be found or created in the following locations for each system:

### Linux/macOS

On Linux and macOS, the pip config file is called `pip.conf` and can be found in the following locations:

- **Global:**
    - **Debian-based systems:** Edit or create `pip.conf in the`/etc\` directory.
    - **macOS-based systems:** Edit or create `/Library/Application Support/pip/pip.conf`.
- **User:**
    - **Debian-based systems:** Edit or create the `~/pip/pip.conf` file.
    - **macOS-based systems:** Edit or create the `~/.config/pip/pip.conf` config file.
- **Site:** When loaded in a Python virtual environment, it’s located at `$VIRTUAL_ENV/pip.conf`.

### Windows

On Windows systems, the file is a `pip.ini` and can be found in the following locations:

- **Global:** Edit or create the `C:\ProgramData\pip\pip.ini` file. Note that this file is hidden by default on Windows systems but is writable.
- **User:** Edit or create `pip.ini` in `%APPDATA%\pip\`.
- **Site:** When loaded in a Python virtual environment, edit or create the config file at `%VIRTUAL_ENV%\pip.ini`.

### Config File Contents

This examples assumes using a Python virtual environment pip config file. In an activated virtual environment, edit `$VIRTUAL_ENV/pip.conf` on Debian-based systems or `%VIRTUAL_ENV%\pip.ini` on Windows.

In the config file, you need to update the `proxy` key with your desired HTTP or HTTPS proxy:

```
[global]
proxy = http://45.185.162.203:999
```

After saving the file, the proxy will be applied automatically to all pip commands, removing the need to specify the proxy flag each time:

```bash
(venv) $ pip install boto3
```

For more details on configuration options please see the [project’s documentation](https://pip.pypa.io/en/stable/topics/configuration/).

## Configuring a pip Proxy with Environment Variables

Configuring system environment variables ensures that a proxy is used for pip and all other HTTP requests on the system. This is achieved by setting the `HTTP_PROXY` and `HTTPS_PROXY` variables, which many applications, including pip, rely on as the system's default proxy for handling HTTP requests.

### Linux/macOS

If you’re using a Linux operating system, update the `/etc/environment` file, or if you’re a macOS user, update the `.zshrc` file located in the home directory. Then, update it with new entries for your proxy server:

```
HTTP_PROXY=https://proxyserver:port
HTTPS_PROXY=https://proxyserver:port
```

Resteart your terminal session or the system, and the environment variables will be present.

### Windows

On a Windows system, you can set environment variables with the following commands in a command prompt terminal:

```
setx HTTP_PROXY "https://proxyserver:port" /M
setx HTTPS_PROXY "https://proxyserver:port" /M
```

Restart the command prompt for the changes to take effect.

## Testing the Configuration

After setting up a system-level configuration using either the pip config file or environment variables, test the proxy to ensure it can successfully connect and transmit data.

### Linux/macOS

On Linux/macOS, use the following command:

```bash
$ python -m venv venv
$ source venve/scripts/activate

# for pip config file or environment variables
(venv) $ pip install requests
```

If you ever want to override these settings with a specific proxy, you can fall back on using the CLI flags:

```bash
# pip cli flag
(venv) $ pip install requests --proxy https://proxyserver:port
```

In this command, make sure you update `https://proxyserver:port` with your own proxy.

### Windows

On Windows, use the following command:

```powershell
> python -m venv venv
> .\venv\Scripts\Activate.bat
(venv) > pip install requests
```

These settings can always be overridden using the pip CLI flags:

```bash
# pip cli flag
(venv) $ pip install requests --proxy https://proxyserver:port
```

### Troubleshooting pip Proxies

When connecting to an HTTP or HTTPS proxy with pip, you may come across the following common issues, particularly if you explore using private proxies or HTTPS proxies due to their enhanced features.

#### Authentication Issues

Authentication issues are commonly seen as a `407 Proxy Authentication Required` error when trying to connect to the proxy with pip. This indicates that the proxy requires a username and password to connect or you have provided the wrong credentials for the proxy.

#### Certificate Issues

When connecting to an HTTPS proxy, you may receive a `Certificate verify failed` error from pip. This indicates that there is an issue with the certificate provided by the proxy server.

If your private proxy server uses a self-signed certificate, you may encounter an error due to the certificate not being verified by a certificate authority. To bypass this, consider using the `--trusted-host` CLI option when connecting to specific domains to ignore self-signed certificate errors.

## Using pip with Rotating Proxies

[Rotating proxies](/solutions/rotating-proxies) help avoid IP bans by automatically switching IP addresses for each request. This mimics multiple users and bypasses restrictions.

You can implement this by randomly selecting proxies from a list. Below is a simple bash script that installs pip packages while rotating through public proxies.

Create the following bash script called `rotate-proxies.sh`:

```bash
proxy_list=(
  'http://45.185.162.203:999'
  'http://177.23.176.58:8080'
  'http://83.143.24.66:80'
)

pip_packages=(
  'requests'
  'numpy'
  'pandas'
)

# Loop through packages and install them
for package in "${pip_packages[@]}"
do
  # Randomly select a proxy from the list
  proxy=${proxy_list[$RANDOM % ${#proxy_list[@]}]}
  echo -e  "\nInstalling $package with proxy $proxy"
  pip install --proxy $proxy $package
done
```

After creating the file, you can run it to download pip packages while rotating through a random proxy for each pip command. Below is a summary of the script’s output:

```
$ ./rotate-proxies.sh 

Installing requests with proxy http://177.23.176.58:8080
Collecting requests
  Downloading requests-2.32.3-py3-none-any.whl.metadata (4.6 kB)

….

Downloading urllib3-2.3.0-py3-none-any.whl (128 kB)
Installing collected packages: urllib3, idna, charset-normalizer, certifi, requests
Successfully installed certifi-2025.1.31 charset-normalizer-3.4.1 idna-3.10 requests-2.32.3 urllib3-2.3.0

Installing six with proxy http://45.185.162.203:999
Collecting numpy
 Downloading numpy-2.2.2-cp313-cp313-macosx_14_0_x86_64.whl.metadata (62 kB)

…

Installing collected packages: numpy
Successfully installed numpy-2.2.2

Installing pandas with proxy http://83.143.24.66:80
Collecting pandas
  Downloading pandas-2.2.3-cp313-cp313-macosx_10_13_x86_64.whl.metadata (89 kB)

….

Installing collected packages: pytz, tzdata, six, python-dateutil, pandas
Successfully installed pandas-2.2.3 python-dateutil-2.9.0.post0 pytz-2025.1 six-1.17.0 tzdata-2025.1
```

## Benefits of Using Proxies with pip

Proxies enable developers to bypass network restrictions, access blocked resources, and optimize package download speeds. Private proxies enhance security by masking identity while also offering caching and faster connections.

Unlike VPNs, which encrypt all internet traffic for greater privacy but may introduce latency, proxies serve as a lightweight alternative for pip requests. They provide a faster and more efficient way to manage dependencies without the performance overhead of a VPN.

## Common Mistakes and Best Practices

When using proxies with pip, it's crucial to avoid common mistakes that could lead to security vulnerabilities. Issues such as incorrect proxy URLs or improperly formatted addresses—such as missing or incorrect HTTP/HTTPS protocols—can disrupt connections to package repositories.

A major security risk is hard-coding proxy credentials (e.g., usernames and passwords) in source code, scripts, or CI/CD pipeline configurations. If the code is shared or exposed, unauthorized access to the proxy may occur. Compromised credentials can lead to misuse, resulting in increased costs or potential exploitation by cyberattacks.

To avoid these mistakes, it’s recommended that proxy credentials are kept secure by storing them in environment variables or encrypted configuration files rather than directly in code. Additionally, you must test proxy connectivity before using pip to ensure proper setup and avoid runtime errors. Using tools like [curl](https://curl.se/) or [ping](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/ping), you can verify proxy performance before putting it into service. This enables a smoother package management experience.

## Using Bright Data Proxies

Bright Data provides a range of IP addresses, including residential, data centers, and mobile devices. It can help you easily create proxies for your project’s needs. Let’s create a private residential proxy that you can use with pip to access packages via a different IP address.

Start by signing up for a free Bright Data account. Then navigate to the [user dashboard](/cp/start/).

On the side menu, click on **Proxies & Scraping**:

![Bright Data proxies](https://brightdata.com/wp-content/uploads/2025/02/Bright-Data-proxies-1024x521.png)

After the form loads, set up a new residential proxy. Using the default settings will assign you a shared IP address, which is used by multiple Bright Data users.

![Creating a residential proxy](https://brightdata.com/wp-content/uploads/2025/02/Creating-a-residential-proxy-1024x520.png)

If you need an IP address from a specific region, you can choose the desired country during setup.

Once the proxy is created, you'll be redirected to a dashboard displaying the endpoint and authentication details. Be sure to note the username, password, and server address.

![Proxy dashboard](https://brightdata.com/wp-content/uploads/2025/02/Proxy-dashboard-1024x520.png)

Test their availability of the endpoint values by using the `--proxy` flag:

```bash
$ pip install pandas \
    --trusted-host pypi.org \
    --trusted-host files.pythonhosted.org \
    --proxy https://username:[email protected]:33335
```

Because the Bright Data proxy uses a self-signed certificate, you can use the `trusted-host` flag to whitelist `pypi.org` and `files.pythonhosted.org` as trusted domains.

## Conclusion

Configuring a proxy for pip is simple, with multiple options like CLI flags, a pip config file, and environment variables. However, public proxies have limitations and aren't ideal for large workloads or production use. For a more reliable solution, Bright Data offers residential and [datacenter IPs](https://brightdata.com/proxy-types/datacenter-proxies), as well as fast, stable connections and advanced tools for web scraping and data collection. Sign up for free to get started.