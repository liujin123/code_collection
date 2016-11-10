RPC属于函数级别的远程调用，其多是通过HTTP传输数据，数据形式有XML、JSON、序列化数据等。
## server.py 
···  from SimpleXMLRPCServer import SimpleXMLRPCServer
  def add(x, y):
      return x + y
  if __name__ == '__main__':
      s = SimpleXMLRPCServer(('127.0.0.1', 8080))
      s.register_function(add)
      s.serve_forever() 
··· 
## client.py 
  from xmlrpclib import ServerProxy
  if __name__ == '__main__':
      s = ServerProxy("http://127.0.0.1:8080")
      print s.add1(15,4) 

## client 请求的xml文件 
<?xml version='1.0' ?>
<methodCall>
    <methodName>
        add
    </methodName>
    <params>
        <param>
            <value>
                <int> 3 </int>
                </value>
        </param>
        <param>
            <value>
                <int> 4 </int>
            </value>
        </param>
    </params>
</methodCall> 

## server 应答的xml文件 
<?xml version='1.0' ?>
<methodResponse>
    <params>
        <param>
            <value>
                <int> 19 </int>
            </value>
        </param>
    </params>
</methodResponse>
