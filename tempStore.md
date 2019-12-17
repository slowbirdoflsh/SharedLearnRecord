```java
packageManager.checkPermission("android.permission.RECEIVE_BOOT_COMPLETED", pkgName);
// 如何package用于该权限则返回GRANTED 否则DENIED
```

```java
// 获取正在运行的app信息
// 5.1.+ 通过访问/proc路径下进程文件获取
// 5.0- 直接使用ActivityManager.getRunningAppProcesses()获取

// 获取正在运行的服务
// 8.0- 直接使用ActivityManager.getRunningServices(int)获取正在运行的服务
// 8.0+ ActivityManager.getRunningServices(int)只会返回自己

// 获取程序占用内存情况
// ActivityManager.getProcessMemoryInfo(int[] pids):MemoryInfo[] 返回输入进程的内存使用情况
// MemoryInfo.getTotalPss():int 返回pss内存 实际使用的物理内存 （VSS/RSS/PSS/USS）

// 杀死进程
// ActivityManager.killBackgroundProcesses(String packageName) 
```

Android 5.0以上的getRunningTasks失效，该方法可以获得在前台运行的系统进程。可以用getRunningAppProcesses方法暂时替代。
android6.0以上的getRunningAppProcesses也失效，系统关闭了三方软件对系统进程的访问，出于安全考虑。


```java
// 垃圾清理

// 系统缓存
// 获取系统所有已安装应用
mContext.getPackageManager().getInstalledApplications(PackageManager.GET_UNINSTALLED_PACKAGES | PackageManager.GET_ACTIVITIES);
// 查询指定包名的缓存大小
// Android 8.0+ 通过包名获取到uid 再通过uid和uuid获取存储信息
ApplicationInfo ai = mContext.getPackageManager().getApplicationInfo(pkgName, PackageManager.GET_META_DATA);
// uuid为存储卷id
// 系统存储信息管理
StorageStatsManager storageStatsManager = (StorageStatsManager) mContext.getSystemService(Context.STORAGE_STATS_SERVICE);
// 系统存储功能管理
StorageManager storageManager = (StorageManager)  mContext.getSystemService(Context.STORAGE_SERVICE);
List<StorageVolume> storageVolumes = storageManager.getStorageVolumes(); // items
uuid = UUID.fromString(item.getUuid());
StorageStats storageStats = storageStatsManager.queryStatsForUid(uuid, uid);
// 获取缓存大小
storageStats.getCacheBytes()

// Android 8.0- 通过反射获取缓存
Method getPackageSizeInfo = mPackageManager.getClass().getMethod("getPackageSizeInfo", String.class, IPackageStatsObserver.class);
// mStatsObserver = new IPackageStatsObserver.Stub() { }
getPackageSizeInfo.invoke(mPackageManager, pkgName, mStatsObserver);

// 应用缓存
// 获取所有已经安装的应用信息
List<PackageInfo> packageInfos = context.getApplicationContext().getPackageManager().getInstalledPackages(0);
// 获取已安装应用的缓存存储路径
String rootPath = Environment.getDataDirectory().getAbsolutePath() + File.separator;
if (Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)) {
    // 取得sdcard文件路径
    rootPath = Environment.getExternalStorageDirectory().getAbsolutePath() + File.separator;
}
// 通过删除文件及文件夹来清理缓存

// 外部存储设备
// 直接递归遍历外部存储设备 寻找 1.无用的安装包 2.log文件 3.临时文件 4.相册缓存垃圾
// 相册缓存垃圾 /DCIM/.thumbnails
// 使用后缀匹配 .apk .log .tmp

// 使用删除文件和文件夹作为清理
```