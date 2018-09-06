# elasticsearch 服务器端安装(docker)

## 安装elasticsearch-5.6.10
由于spring-boot-2.0.4，对应的elasticsearch版本默认是5.6.10,因此安装该版本
<pre>
docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:5.6.10
</pre>

