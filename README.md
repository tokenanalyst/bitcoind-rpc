# bitcoin-rpc

## Supported methods

| Bitcoind RPC method  | description  |  bitcoin-rpc method |
|---|---|---|
| getblockhash  | Gets the block hash at a specific height  |  getBlockHash(height: Long) |
| getbestblockhash  |  Gets the block tip hash | getBestBlockHash()  |
| getblock  | Gets the block with transaction ids  |  getBlock(hash: String) |
| getblock  | Gets the block with transaction ids  |  getBlock(height: Long) |

## Example

```
  import cats.effect.{ExitCode, IO, IOApp}
  import scala.concurrent.ExecutionContext.global
  
  object Main extends IOApp {

    def run(args: List[String]): IO[ExitCode] = {
    
      implicit val config = Config("127.0.0.1","user","password")
      implicit val ec = global
      
      // opening up resources for HTTP and ZeroMQ
      BitcoinRPC.openAll().use {
        case (http, zmq) =>
          for {
            
            // retrieving a simple block by hash
            block <- BitcoinRPC.getBlock(http, 
            "00000000000000000012fb9247e97999280cc8c1aedde0e2f2e3e7383f909e20")
            
            // listening for new blocks
            newBlockHash <- zmq.nextBlock()
            
            _ <- IO { println(block) }
          } yield ExitCode(0)
      }
    }
  }
```