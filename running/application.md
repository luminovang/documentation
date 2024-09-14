# Launching Local Development Server

***

## Overview

Luminova built-in development server, leveraging PHP's development server functionality. This is useful for development and testing your application in a controlled environment.

***

## Introduction

Luminova provides a convenient built-in development server, which leverages PHP's native web server functionality. This feature allows you to quickly set up a server for local development and testing, giving you a controlled environment to build and refine your application. You can opt to use Luminova’s built-in server or continue using platforms like `XAMPP` or `WAMPP`, depending on your workflow.

#### Features:

- **Quick Setup**: Launch your development environment with a single `novakit` command.
- **Isolated Environment**: Develop in a stable, controlled space tailored for local testing.
- **Flexible Configuration**: Customize the host and port to fit your network and workflow.
- **Real-Time Testing**: Seamlessly expose your server to other devices for immediate feedback and testing across the network.

***

### Command Help

To display help related information for development server run the below command.

```bash
php novakit server --help
```

### Server Command

The `server` command starts the Luminova PHP development server. To start server, navigate to your project main directory and run the following command:

```bash
php novakit server
```

> This will start the server, allowing you to access your application over the browser by visiting `http://localhost:8080`.

***

### Command Options

You can customize the server startup with the following options:

- `-b, --php`: - Specify the PHP binary location.
- `-h, --host`: - Specify the development hostname.
- `-p, --port`: - Specify the port for the development server.
- `-t, --testing`: - Start the server with the network address for testing on other devices

***

Specifying a hostname and port:

```bash
php novakit server --host=127.0.0.1 --port=8081
```

> Passing your hostname and port will start the server and you can now view your application in your browser at `http://127.0.0.1:8081`.

Specifying the `PHP` version to use.

```bash
php novakit server --php=/path/to/php.binary
```
> The value should be the path to the PHP executable binary version you want to use:

***

### Third Party Server

If you prefer to use third-party development servers like `XAMPP` or `WAMPP`, simply install and start the respective application services. Then, navigate to the `htdocs` directory in `XAMPP` or the `www` directory in `WAMPP` and create your project directory (e.g., myproject.com).

You can access your project by navigating to `http://localhost/myproject.com/public/` or `http://127.0.0.1/myproject.com/public/`, in your web browser, which will launch your project.

***

### Real-Time Testing

To test your application on other devices connected to the same network during development, you need to expose your Luminova development server to those devices, such as your mobile phone or another computer. Follow these steps to make your local machine’s IP address accessible across the network.

#### Start with Testing Command

To start the server for testing on other devices, run command with `--testing` flag and your desired port number:

```bash
php novakit server --port=8080 --testing
```

#### Manual Testing Command

If the above command fails for any reason, you can manually set up the server as follows: 

1. **Find Your Computer’s Local IP Address:**
   - On **Windows**, open the Command Prompt and run:
     ```bash
     ipconfig
     ```
     Look for the **"Wireless LAN adapter"** (for Wi-Fi) or **"Ethernet adapter"** (for a wired connection). Find the **IPv4 Address** (e.g., `192.168.1.100`).
   
   - On **macOS** or **Linux**, open the Terminal and run:
     ```bash
     ifconfig
     ```
     Look for the **"inet"** address under the relevant network adapter, which is typically something like `192.168.x.x`.

2. **Start the Luminova Development Server with IP Binding:**
   Use the `novakit` command to start the server, binding it to your local IP address. Run the following command, replacing `192.168.x.x` with your actual IP and setting the port to your desired value (e.g., `8081`):
   ```bash
   php novakit server --host=192.168.x.x --port=8081
   ```

   - `192.168.x.x` is your local IP address.
   - `8081` is the port you assigned.
   
   This command will run the development server at `http://192.168.x.x:8081`.

3. **Access from Another Device (e.g., Your Mobile):**
   On your mobile device, ensure it's connected to the same `Wi-Fi` network. Open a browser and navigate to:
   ```bash
   http://192.168.x.x:8081
   ```
   You should now be able to view your Luminova development site from any of your devices.

4. **Optional - Hostname Mapping (Using a Custom Domain, e.g., `foo.bar.baz`):**
   If you want to use a custom domain like `foo.com`, you can set up hostname mapping:

   - On your mobile device:
     - For **iOS** or **Android**, use a tool or app like **Localhost** or **Dnsmasq** to map the domain to your computer’s IP address.

   - On **Windows/macOS/Linux**:
     Manually edit the **hosts** file:
     - On **Windows**, the file is located at `C:\Windows\System32\drivers\etc\hosts`.
     - On **macOS/Linux**, it's located at `/etc/hosts`.

     Add the following entry:
     ```bash
     192.168.x.x foo.com
     ```
     This will map `foo.com` to your computer’s IP, allowing you to access the server via `http://foo.com:8081`.

> **Important Notes:**
> 
> **Firewall Configuration:** Ensure your firewall allows incoming connections on the port you're using (e.g., `8081`).
> 
> **Network Connection:** Your development computer and other testing devices must be connected to the same network (Wi-Fi) for this to work.