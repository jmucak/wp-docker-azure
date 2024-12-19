# WP Docker Azure setup

## Azure blob setup
On first run, you need to set up the Azure blob storage. You can do this by running the following commands:

### run azure-cli container
```docker-compose exec -it azure-cli bash```

### create azure container named wp-container
```
az storage container create --name wp-container --connection-string="DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://host.docker.internal:10000/devstoreaccount1;"
```

### set container permission
```bash
az storage container set-permission --name wp-container --public-access blob --connection-string="DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://host.docker.internal:10000/devstoreaccount1;"
```

### to list all azure blob containers
```
az storage container list --output table --connection-string="DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://host.docker.internal:10000/devstoreaccount1;"
```

## Installing plugins

### Using composer
Position yourself in the wordpress container
```docker-compose exec -it wordpress /bin/bash```
and run
```composer install```

### Using WP-CLI
Position yourself in the wordpress container
```docker-compose exec -it wordpress /bin/bash```

and run
```
wp plugin install contact-form-7 --activate --allow-root
wp plugin install windows-azure-storage --activate --allow-root
```

To install ACF Pro plugin, you need to have a valid license key. You can get it from the [ACF website](https://www.advancedcustomfields.com/my-account/). After you get the key, you can run the following command:

```
wget -O $(wp plugin path --allow-root)/acf-pro.zip 'https://connect.advancedcustomfields.com/v2/plugins/download?p=pro&k={LICENSE_KEY}'
wp plugin install $(wp plugin path --allow-root)/acf-pro.zip --activate --allow-root
rm -f $(wp plugin path --allow-root)/acf-pro.zip
```

## Note (only for local development)
After windows azure storage plugin is installed, you need to change the connection string in the plugin file. 
The file is located at `includes/class-windows-azure-rest-api-client.php` and the method is `set_connection_string`. 
The connection string should be set to the following:
```
$this->_connection_string = 'DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://host.docker.internal:10000/devstoreaccount1;';
```
Method will look like this:
```
public function set_connection_string() {
    $this->_connection_string = 'DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://host.docker.internal:10000/devstoreaccount1;';
}
```
This is only temporary solution