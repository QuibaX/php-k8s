# Persistent Volume

- [Official Documentation](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)

## Example

### PV Creation

```php
$pv = $cluster->persistentVolume()
    ->setName('disk-1')
    ->setSelectors(['matchLabels' => ['app' => 'bigdata'])
    ->setSource('awsElasticBlockStore', [
        'fsType' => 'ext4',
        'volumeID' => 'vol-xxxxx',
    ])
    ->setCapacity(10, 'Gi')
    ->setAccessModes(['ReadWriteOnce'])
    ->setMountOptions(['nfsvers=4.1'])
    ->setStorageClass('gp2')
    ->create();
```

You can pass the storage class as a `RenokiCo\PhpK8s\Kinds\K8sStorageClass` instance:

```php
$sc = $cluster->storageClass()
    ->setName('sc1')
    ->setProvisioner('csi.aws.amazon.com')
    ->setParameters(['type' => 'sc1'])
    ->setMountOptions(['debug'])
    ->create();

$pv = $cluster->persistentVolume()
    ->setName('disk-1')
    ->setSelectors(['matchLabels' => ['app' => 'bigdata'])
    ->setSource('awsElasticBlockStore', [
        'fsType' => 'ext4',
        'volumeID' => 'vol-xxxxx',
    ])
    ->setCapacity(10, 'Gi')
    ->setAccessModes(['ReadWriteOnce'])
    ->setMountOptions(['nfsvers=4.1'])
    ->setStorageClass('gp2');

$pv->setStorageClass($sc)->create();
```

While the PersistentVolume kind has `spec`, you can avoid writing this:

```php
$pv->setAttribute('spec.capacity.storage', '10Gi');
```

And use the `setSpec()` method:

```php
$pv->setSpec('capacity.storage', [...]);
```

Dot notation is supported:

```php
$pv->setSpec('some.nested.path', [...]);
```

### Retrieval

```php
$pv = $cluster->getPersistentVolumeByName('disk-1');

$capacity = $pv->getCapacity(); // "10Gi"
```

Retrieving the spec attributes can be done like the `setSpec()` method:

```php
$pv->getSpec('volumeMode', 'Block');
```

The second value for the `getSpec()` method is the default value, in case the found path is not existent.

Dot notation is supported:

```php
$pv->getSpec('some.nested.path', []);
```