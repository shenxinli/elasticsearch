# elasticsearch 服务器端安装(docker)

## 安装elasticsearch-5.6.10
由于spring-boot-2.0.4，对应的elasticsearch版本默认是5.6.10,因此安装该版本
<pre>
docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:5.6.10
</pre>

## 安装docker-compose
<pre>
sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
</pre>

编辑docker-compose.yml
<pre>
vi docker-compose.yml
</pre>
内容如下
<pre>
version: '2'
services:
  elasticsearch1:
    image: docker.elastic.co/elasticsearch/elasticsearch:5.6.10
    container_name: elasticsearch1
    environment:
      - cluster.name=docker-cluster
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    mem_limit: 1g
    volumes:
      - esdata1:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - esnet
  elasticsearch2:
    image: docker.elastic.co/elasticsearch/elasticsearch:5.6.10
    environment:
      - cluster.name=docker-cluster
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - "discovery.zen.ping.unicast.hosts=elasticsearch1"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    mem_limit: 1g
    volumes:
      - esdata2:/usr/share/elasticsearch/data
    networks:
      - esnet

volumes:
  esdata1:
    driver: local
  esdata2:
    driver: local

networks:
  esnet:
</pre>

## 安装 docker-machine
<pre>
$ base=https://github.com/docker/machine/releases/download/v0.14.0 &&
  curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/tmp/docker-machine &&
  sudo install /tmp/docker-machine /usr/local/bin/docker-machine
</pre>

## 验证安装
<pre>
curl -u elastic http://127.0.0.1:9200/_cat/health
</pre>

注意：运行后要求输入密码
<pre>
Enter host password for user 'elastic':
</pre>
这里初始密码是:
<pre>
changeme
</pre>

## 修改默认密码
curl -XPUT -u elastic 'http://localhost:9200/_xpack/security/user/elastic/_password' -d '{"password" : "123456"}'
