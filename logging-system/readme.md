The Chain of Responsibility pattern is a design pattern that allows a group of objects to handle a request by passing it along a dynamic chain of objects. This pattern can be used to implement a logging system in Java.

Here's how you can implement a logging system using the Chain of Responsibility pattern in Java:

Create an abstract class AbstractLogger that defines the interface for a logger and contains a reference to the next logger in the chain. The class should have methods such as setNextLogger() and logMessage().

Create concrete classes such as ConsoleLogger, FileLogger, and EmailLogger that extend the AbstractLogger class. These classes should implement the logMessage() method to handle logging requests.

In the logMessage() method of each class, check the level of the message and pass it to the next logger in the chain if it is not able to handle it.

Create a class LoggerChain that initializes the chain of loggers and specifies the order in which they should handle requests.

Here is an example of a logging system implemented using the Chain of Responsibility pattern in Java:


```
abstract class AbstractLogger {
   public static int INFO = 1;
   public static int DEBUG = 2;
   public static int ERROR = 3;

   protected int level;

   //next element in chain or responsibility
   protected AbstractLogger nextLogger;

   public void setNextLogger(AbstractLogger nextLogger){
      this.nextLogger = nextLogger;
   }

   public void logMessage(int level, String message){
      if(this.level <= level){
         write(message);
      }
      if(nextLogger !=null){
         nextLogger.logMessage(level, message);
      }
   }

   abstract protected void write(String message);
}

class ConsoleLogger extends AbstractLogger {
   public ConsoleLogger(int level){
      this.level = level;
   }

   @Override
   protected void write(String message) {    
      System.out.println("Standard Console::Logger: " + message);
   }
}

class ErrorLogger extends AbstractLogger {
   public ErrorLogger(int level){
      this.level = level;
   }

   @Override
   protected void write(String message) {    
      System.out.println("Error Console::Logger: " + message);
   }
}

class FileLogger extends AbstractLogger {
   public FileLogger(int level){
      this.level = level;
   }

   @Override
   protected void write(String message) {    
      System.out.println("File::Logger: " + message);
   }
}

class LoggerChain {
   public static AbstractLogger getChainOfLoggers(){
      AbstractLogger errorLogger = new ErrorLogger(AbstractLogger.ERROR);
      AbstractLogger fileLogger = new FileLogger(AbstractLogger.DEBUG);
      AbstractLogger consoleLogger = new ConsoleLogger(AbstractLogger.INFO);

      errorLogger.setNextLogger(fileLogger);
      fileLogger.setNextLogger(consoleLogger);

      return errorLogger;  
   }
}

class ChainPatternDemo {
   public static void main(String[] args) {
      AbstractLogger loggerChain = LoggerChain.getChainOfLoggers();

      loggerChain.logMessage(AbstractLogger.INFO, "This is an information.");
      loggerChain.logMessage(AbstractLogger.DEBUG, "This is an debug level information.");
      loggerChain.logMessage(AbstractLogger.ERROR, "This is an error information.");
   }
}


```

The code above creates an instance of the LoggerChain class which initializes the chain of loggers and specifies the order in which they should handle requests, also in the main class, we are using the logMessage method with different level to see the output of each logger in the chain.

here are some sample test cases and inputs for the logging system implemented using the Chain of Responsibility pattern in Java:


```
public static void main(String[] args) {
    AbstractLogger loggerChain = LoggerChain.getChainOfLoggers();

    // Test case 1: Logging an information message
    loggerChain.logMessage(AbstractLogger.INFO, "Application started");

    // Test case 2: Logging a debug message
    loggerChain.logMessage(AbstractLogger.DEBUG, "Debug information: variable x = 5");

    // Test case 3: Logging an error message
    loggerChain.logMessage(AbstractLogger.ERROR, "Error: unable to connect to database");
}


```

In the test cases above, we are using the logMessage() method to log messages at different levels (INFO, DEBUG,ERROR), the log message will be passed through the chain of loggers, and each logger will check if it is able to handle the message based on the level passed to the method and if it's not able to handle it, it will pass it to the next logger in the chain.


