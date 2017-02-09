## PHP 文件加锁解决并发的一点记录

```
// 简单的文件计数器
function pageCount($fileName){
    if ($fp = fopen($fileName, 'r+')) {
        $startTime = microtime();
        do {
            $canWrite = flock($fp, LOCK_EX | LOCK_NB);//LOCK_EX排它锁，LOCK_NB非阻塞
            if (!$canWrite) {
                usleep(round(rand(0, 100) * 1000));
            }
        } while ((!$canWrite) && ((microtime() - $startTime) < 1000));
        if ($canWrite) {
            $count = intval(fgets($fp));
            ++$count;
            ftruncate($fp,0); // 将文件截断到给定的长度 
            rewind($fp); // 倒回文件指针的位置 
            fwrite($fp, $count);
            flock($fp , LOCK_UN);
        }
        fclose($fp);
    }
}
```

使用场景，一个发红包的系统中，使用文件锁来控制发红包频率（在没有使用redis等缓存系统时的备选方案）

```
$lockfile = dirname(__FILE__) . '/redpac.lock';
if ($fp = fopen($lockfile, 'w')) {
    if (flock($fp, LOCK_EX | LOCK_NB)) {
        usleep(100000);// 锁住文件1/10秒 此函数windows不支持，即每秒只进入10次处理程序
        flock($fp, LOCK_UN); //解锁文件
        fclose($fp);
    } else { //未获取到锁时直接返回，不进入后面的处理程序
        //flock($fp, LOCK_UN);
        fclose($fp);
        return ;
    }
}
// 处理程序
```