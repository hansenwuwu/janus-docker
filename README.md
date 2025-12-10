# Janus Docker

## Configuration Setup

### 1. Extract Default Configuration Files

The `janus.jcfg` configuration file can be obtained from the original `canyan/janus-gateway` Docker image.

```bash
# Start the default Janus container
docker run -d --name janus-temp canyan/janus-gateway:latest

# Access the container
docker exec -it janus-temp bash

# Locate the configuration file at:
# /usr/local/etc/janus/janus.jcfg

# Copy configuration files to your workspace
docker cp janus-temp:/usr/local/etc/janus/janus.jcfg ./config/

# Remove temporary container
docker stop janus-temp
docker rm janus-temp
```

### 2. Configure janus.jcfg

There are two critical settings in `janus.jcfg`:

#### a. RTP Port Range
```conf
media: {
    rtp_port_range = "20000-20200"
}
```
**Important**: This range MUST match the UDP port mapping in `docker-compose.yml`:
```yaml
ports:
  - "20000-20200:20000-20200/udp"
```

#### b. NAT 1:1 Mapping
```conf
nat: {
    nat_1_1_mapping = "192.168.3.53"  # Set to your current IP address
}
```

## Starting Janus Server

```bash
# Start in detached mode
docker-compose up -d

# Or start with logs visible
docker-compose up
```

---

## Verify Installation

### Check Container Status
```bash
docker ps | grep janus
docker logs janus-gateway
```

### Test API Endpoints
```bash
# HTTP API
curl http://localhost:8088/janus/info

# Should return JSON with server information
```

---

## API Endpoints

- **HTTP API**: `http://localhost:8088/janus`
- **WebSocket API**: `ws://localhost:8188`
- **Admin API**: `http://localhost:8089/admin`

---

## Running Demo Examples

### 1. Clone Janus Gateway Repository
```bash
git clone https://github.com/meetecho/janus-gateway.git
cd janus-gateway
```

### 2. Use HTML Demos
Navigate to the demos directory:
```
cd ./html/demos
```

### 3. Configure Demo Files
In the demo HTML files, update all instances of `:8088/janus` with your server's IP address:

```javascript
// Before
var server = "http://localhost:8088/janus";

// After (example for local network)
var server = "http://192.168.3.53:8088/janus";
```

---

## Troubleshooting

### ICE Connection Failed
1. Verify `nat_1_1_mapping` is set to your correct IP
2. Ensure UDP port range is properly mapped
3. Check firewall settings allow UDP ports 20000-20200
