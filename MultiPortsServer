import java.io.*;
import java.net.*;
import java.nio.*;
import java.nio.channels.*;
import java.util.*;

public class MultiPortsServer {
  private int ports[];
  private ByteBuffer eBuffer = ByteBuffer.allocate( 1024 );
  public MultiPortsServer( int ports[] ) throws IOException {
    this.ports = ports;
    go();
  }

  private void go() throws IOException {
    Selector selector = Selector.open();
    for (int i = 0; i < ports.length; ++i) {
      ServerSocketChannel ssc = ServerSocketChannel.open();
      ssc.configureBlocking( false );
      ServerSocket ss = ssc.socket(); // peer
      InetSocketAddress address = new InetSocketAddress( ports[i] );
      ss.bind( address );
      SelectionKey key = ssc.register( selector, SelectionKey.OP_ACCEPT );
    }

    while (true) {
      int num = selector.select();

      Set selectedKeys = selector.selectedKeys();
      Iterator it = selectedKeys.iterator();

      while (it.hasNext()) {
        SelectionKey key = (SelectionKey) it.next();

        if ((key.readyOps() & SelectionKey.OP_ACCEPT) == SelectionKey.OP_ACCEPT) {
          ServerSocketChannel ssc = (ServerSocketChannel) key.channel();
          SocketChannel sc = ssc.accept();
          sc.configureBlocking( false );
          SelectionKey newKey = sc.register( selector, SelectionKey.OP_READ );
          it.remove();
		  
        } else if ((key.readyOps() & SelectionKey.OP_READ) == SelectionKey.OP_READ) {
          SocketChannel sc = (SocketChannel) key.channel();

          int bytes = 0;
          while (true) {
            eBuffer.clear();
            int read = sc.read( eBuffer );
            if (read <= 0) {
              break;
            }
            eBuffer.flip();
            sc.write( eBuffer );
            bytes += read;
          }
          it.remove();
        }
      }
    }
  }

  // Test
  static public void main( String args[] ) throws Exception {
    if (args.length <= 0) {
      System.exit( 1 );
    }
	
    int ports[] = new int[args.length];
    for (int i = 0; i < args.length; ++i) {
      ports[i] = Integer.parseInt( args[i] );
    }

    new MultiPortsServer( ports );
  }
}
