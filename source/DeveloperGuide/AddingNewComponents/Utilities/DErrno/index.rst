==================================
Handling errors within DIRAC
==================================

The choice was made not to use exception within DIRAC. The return types are however standardized. 

----------------------------------
S_ERROR
----------------------------------

This object is now to be phased out by the `DError`_ object.

The *S_ERROR* object is basicaly a dictionary with the *'OK'* key to *False*, and a key *'Message'* which contains the actual error message.



.. code-block:: python
  
   from DIRAC import S_ERROR
   
   res = S_ERROR("What a useful error message")
   
   print res
   # {'Message': 'What a useful error message', 'OK': False}
   


There are two problems with this approach:

  * It is difficult for the caller to react based on the error that happened
  * The actual error cause is often lost because replaced with a more generic error message that can be parsed

.. code-block:: python
  
  def func1():
    # Error happening here, with an interesting technical message
    return S_ERROR('No such file or directory')
    
  # returns a similar, but only similar error message 
  def func2():
    # Error happening here, with an interesting technical message
    return S_ERROR('File not found')
 
   
  def main():
    ret = callAFunction()
    
    if not res['OK']:
      if 'No such file' in res['Message']:
        # Handle the error properly
        # Unfortunately not for func2, eventhough it is the same logic
        

A similar logic is happening when doing the bulk treatment. Traditionally, we have for bulk treatment an *S_OK* returned, which contains as value two dictionaries called 'Successful' and 'Failed'. The 'Failed' dictionary contains for each item an error message.

.. code-block:: python

  def doSomething(listOfItems):
    successful = {}
    failed = {}
  
    for item in listOfItems:
      # execute an operation
      
      res = complicatedStuff(item)
      
      if res['OK']:
        successful[item] = res['Value']
      else:
        print "Oh, there was a problem: %s"%res['Message']
        failed[item] = "Could not perform doSomething"
      
    return S_OK('Successful' : successful, 'Failed : failed)
   
   
.. _DError:

----------------------------------
DError
----------------------------------

In order to address the problems raised earlier, the DError object has been created. It contains an error code, as well as a technical message. The human readable generic error message is inherent to the error code, in a similar way to what *os.strerror* is doing.


.. code-block:: python
  
  from DIRAC import DError
  import errno
  
  def func1():
    # Error happening here, with an interesting technical message
    return DError(errno.ENOENT, 'the interesting technical message')


The interface of this object is fully compatible with S_ERROR

.. code-block:: python

  res = DError(errno.ENOENT, 'the interesting technical message')
  
  print res
  # No such file or directory ( 2 : the interesting technical message)

  print res['OK']
  # False
  
  print res['Message']
  # No such file or directory ( 2 : the interesting technical message)
  
  
  # Extra info of the DError object
  
  print res.errno
  # 2
  
  print res.errmsg
  # the interesting technical message
  

Another very interesting feature of the DError object is that it keeps the call stack when created, and the stack is displayed in case the object is displayed using *gLogger.debug*

The *Derror* object replaces S_ERROR, but should also be used in the *Failed* dictionary for bulk treatments.

Handling the error
~~~~~~~~~~~~~~~~~~~~~~

Since obviously we could not change all the *S_ERROR* at once, the *DError* object has been made fully compatible with the old system.
This means you could still do something like 

.. code-block:: python
 
  res = func1()
  if not res['OK']:
    if 'No such file' in res['Message']:
      # Handle the error properly

There is however a much cleaner method which consists in comparing the error returned with an error number, such as ENOENT. 
Since we have to be compatible with the old system, a utility method has been written *'cmpError'*.


.. code-block:: python
 
  from DIRAC import DErrno 
  import errno
 
  res = func1()
  if not res['OK']:
    # This works whether res is an S_ERROR or a DError object
    if DErrno.cmpError(res, errno.ENOENT):
      # Handle the error properly
      

An important aspect and general rule is to NOT replace the object, unless you have good reasons

.. code-block:: python
 
  # Do that ! 
  def func2():
    res = func1()
    if not res['OK']:
      # I cannot handle it, so I return it AS SUCH
      return res
      
  # DO NOT DO THAT
  def func2():
    res = func1()
    if not res['OK']:
      return S_ERROR("func2 failed with %s"%res['Message'])
  

      

Error code
~~~~~~~~~~~~~~~~~~~~~~

The best practice is to use the errors at your disposal in the standard python module errno.
If, for a reason or another, no error there would match your need, there are already "DIRAC standard" errors defined in *DErrno* ( Core/Utilities/DErrno.py)

In case the error you would need does not exist yet as a number, there are 4 things you need to do:
  * Think whether it really does not match any existing error number
  * Update the *dErrorCode* dictionary in DErrno.py
  * Update the *dStrError* dictionary in DErrno.py
  * Think again whether you really need that
  
Refer to the python file for more detailed explanations on these two dictionary.

There is a third dictionary that can be filled, which is called *compatErrorString*. This one is used for error comparison. To illustrate its purpose suppose the following existing code:

.. code-block:: python
 
  def func1():
    [...]
    return S_ERROR("File does not exist")
  
  def main():
    res = func1()
    if not res['OK']:
      if res['Message'] == "File does not exist":
        # Handle the error properly
        

You happen to modify *func1* and decide to return the appropriate DError object, but do not change the *main* function:

.. code-block:: python
 
  def func1():
    [...]
    return DError(errno.ENOENT, 'technical message')
  
  def main():
    res = func1()
    if not res['OK']:
      if res['Message'] == "File does not exist":
        # Handle the error properly

        
The test done in the main function will not be satisfied anymore. The cleanest way is obviously to update the test, but if ever this would not be possible,
for a reason or another, you could add an entry in the *compatErrorString* which would state that "File does not exist" is *compatible* with errno.ENOENT. 
    

