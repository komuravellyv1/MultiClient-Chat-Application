import java.net.*;
import java.io.*;
import java.util.ArrayList;
import java.util.Hashtable;
import java.util.Iterator;
import java.util.Set;
public class EchoServer {
    static ThreadList threadlist = new ThreadList();  	
    public static void main(String[] args) throws IOException {
        if (args.length != 1) {
            System.err.println("Usage: java EchoServer <port number>");
            System.exit(1);
        }
        //Check Input Validation Here.
        
        int portNumber = Integer.parseInt(args[0]);
        
        try {
            ServerSocket serverSocket =   new ServerSocket(Integer.parseInt(args[0]));
            System.out.println("EchoServer is running at port " + Integer.parseInt(args[0]));

	while(true){

            Socket clientSocket = serverSocket.accept(); 
            System.out.println("A client is connected "); 
            
	EchoServerThread newthread = new EchoServerThread(threadlist,clientSocket); 
	threadlist.addThread(newthread);
	newthread.start();	
	
    }  
        }

	catch (IOException e) {
            System.out.println("Exception caught when trying to listen on port "
                + portNumber + " or listening for a connection");
            System.out.println(e.getMessage());
        }
    }
}
class EchoServerThread extends Thread{
	private Socket clientSocket = null;
	private ThreadList threadlist = null;
	private PrintWriter out = null;
	private BufferedReader in = null;
	private String newusername;
	static ArrayList<String> thread_list = new ArrayList<String>();
	//***************        Constructors       ********************
	public EchoServerThread(Socket socket){
		clientSocket = socket;
	}
	public EchoServerThread(ThreadList threadlist, Socket socket){
	clientSocket = socket;
	this.threadlist = threadlist;
	}
	public EchoServerThread(String newusername) {
		this.newusername = newusername;
	}
	public void send(String message) {
	if (out!=null)
	out.println(message);
	}
	public synchronized void addListofUsers(String newusername) {
		thread_list.add(newusername);
	}
	public synchronized void getListofUsers() {
		for(int i=0;i< thread_list.size();i++) {
			send(thread_list.get(i));
		}
		}
	public void run(){
		System.out.println("A new thread for client is running");
		Hashtable<String, String> hashtable = new Hashtable<String, String>();
			hashtable.put("Venkat","venk@03");
			hashtable.put("Phu","Dayton1");
			hashtable.put("Yesh", "Secure2");
			hashtable.put("Jack", "Secure3");
			hashtable.put("Matt", "Secure4");
	if(threadlist!=null)
	System.out.println("Inside thread:total clients: " + threadlist.getNumberofThreads());
	try{
	  out = new PrintWriter(clientSocket.getOutputStream(), true);                 
      in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
      String inputLine;  
	if(threadlist!=null){
	threadlist.sendToAll("the no of connected clients- "+ threadlist.getNumberofThreads());
	}
            while ((inputLine = in.readLine()) != null) {
            	String command = getCommand(inputLine);
            	while(command.equals("<Join>")) {
                		String newusername = parseUsername(inputLine);
                		this.newusername = newusername;
                		String pass = parsePassword(inputLine);
                		Set<String> keys = hashtable.keySet();
                		while(keys.contains(newusername)) {
                			String value = hashtable.get(newusername);
                			   if(value.equals(pass)) {
                				   send("Hi "+ newusername +" Welcome to chat room!");
                				   addListofUsers(newusername);
                				   threadlist.sendToAll("To All <new message>:"+ newusername + " Joined");
                				   send("************************************");
                				   send("Type <List> To get list of users");
                				   send("Type <Exit> To exit Chat room");
                				   send("Type <Chat>Message -To chat with Everyone");
                				   send("Type <Priv>Private_user:Message -To chat privately");
                				   send("************************************");
                				   break;
                			   		}else {
                			   			send("Invalid username:password"); break;
                			   			}
                			}	
            break;    	}
    if(command.equals("<Chat>")) {
        String str = parseStringMessage(inputLine);
        threadlist.sendToAll("To All <Chat Message>"+ str);
      	}
    else if(command.equals("<Priv>")) {
     		parsePrivateMessage(inputLine);
     	}
 	else if(inputLine.equals("<Exit>")){
                    	threadlist.sendToAll("To All: A client exists, the number of connected client:" + (threadlist.getNumberofThreads()-1));
                    	thread_list.remove(newusername);
                    	threadlist.removeThread(this);
                    	send("Updated Users List: ");
                    	getListofUsers();
                    	clientSocket.close();
		
                    }
 	else if(inputLine.equals("<List>")){
    	send("The List of users in the chat room:");
    	getListofUsers();
    } 
 	}
	}     
	catch (IOException ioe) {
            System.out.println("Exception caught is " + ioe.getMessage());
        }
            }
	
private String getCommand(String data) {
	if(data.isEmpty() || (data.length()<6))
		return "UNKNOWN";
		try {
			String command = data.substring(0, 6).trim();
			return command;
		}catch(Exception e) {
			return "UNKNOWN";
		}
	}
private String parseStringMessage(String logindata) {
	String s = logindata.substring(6);
	return s;
}
private void parsePrivateMessage(String logindata) {
	String s = logindata.substring(6);
	String[] pmsg = s.split(":");
	String p_user = pmsg[0];
	String p_msg = pmsg[1];
	threadlist.sendPrivate(newusername, p_user, p_msg);
}

	    private String parseUsername(String logindata){
	    	String s = logindata.substring(6);
	    	String[] user = s.split(":"); // user array of type String to store new username.
	        return user[0];
	    }
	    private String parsePassword(String logindata){
	    	String st = logindata.substring(6);
	    	String[] pass = st.split(":");
	        return pass[1];
	    }
	    public String getUserName() {
	    	return this.newusername;
	    }
}
class ThreadList{
	//private ... threadlist = //store the list of threads in this variable	
	private ArrayList<EchoServerThread> threadlist = new ArrayList<EchoServerThread>();
	
	public ThreadList()
	{
	}
	public  synchronized int getNumberofThreads(){
	//return the number of current threads
	return threadlist.size();
	}
	public synchronized void addThread(EchoServerThread newthread){
	//add the newthread object to the threadlist	
	threadlist.add(newthread);
	}
	public synchronized void removeThread(EchoServerThread thread){
	//remove the given thread from the threadlist	
	threadlist.remove(thread);	
	}
	public synchronized void sendPrivate(String sender, String username, String message){
        for(EchoServerThread thread : threadlist){
            if(thread.getUserName().equals(username)){
                thread.send("<private> "+sender+ ":" +message);
            }
        }
    }
	public synchronized void sendToAll(String message){
	Iterator<EchoServerThread> threadlistIterator = threadlist.iterator();
	while(threadlistIterator.hasNext()){
	EchoServerThread thread = threadlistIterator.next();
	thread.send(message);
	//ask each thread in the threadlist to send the given message to its client		
	}}
}  

