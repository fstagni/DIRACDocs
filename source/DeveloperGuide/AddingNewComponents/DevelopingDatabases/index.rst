==============================================
Developing Databases
==============================================

Developing databases mean: developing the DB schema 
(with DIRAC, both MySQL and Oracle are supported, with a strong preference for the first) 
and developing the python database class that will interact with the DB itself. A simple example follow::


    """ This is just an example
    """
  
    __RCSID__ = "$Id$"
    
    
    from DIRAC.Core.Base.DB import DB
    
    class MyDB( DB ):
    """ MyDB class
    """
  
    def __init__( self, dbname = None, dbconfig = None, maxQueueSize = 10, dbIn = None ):
      """ The standard constructor takes the database name (dbname) and the name of the
          configuration section (dbconfig)
      """
  
      if not dbname:
        dbname = 'MyDB'
      if not dbconfig:
        dbconfig = 'MySection/MyDB'
  
      if not dbIn:
        DB.__init__( self, dbname, dbconfig, maxQueueSize )
  
  
    def justSetSomething(self, someValue):
      """ Setting a value. The base class DB exposes basic methods to be used for setting and getting values
      """
      request = "INSERT INTO MyDB (someParameter) VALUES (%s)" % someValue
      res = self._update( req )
      if not res['OK']:
        return res
      
      #do something else if needed
