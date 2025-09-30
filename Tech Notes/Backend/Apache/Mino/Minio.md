# docker

docker run -p 9000:9000 --name minio -e "MINIO_ACCESS_KEY=TANG" -e "MINIO_SECRET_KEY=QWE123BNM" -v F:\minio\minio\data:/data -v F:\minio\minio\config:/root/.minio minio/minio server /data


docker run -d -p 9000:9000 -p 9001:9001 --name minio -v D:\minio_data:/data -e "MINIO_ROOT_USER=admin" -e "MINIO_ROOT_PASSWORD=123456" quay.io/minio/minio server /data --console-address ":9001"


https://www.jianshu.com/p/c0bf5facde51

https://gitee.com/WangFuGui-Ma/spring-boot-minio

https://blog.csdn.net/m0_47406832/article/details/125072416

https://www.cnblogs.com/liangyy/p/16092950.html

http://www.wutang.cc/2021/03/30/windows%E4%BD%BF%E7%94%A8docker%E6%90%AD%E5%BB%BAminio/