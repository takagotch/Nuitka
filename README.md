### nuitka
---
https://github.com/Nuitka/Nuitka

http://nuitka.net/

```py
// nuitka/finalizations/FinalizeMarkups.py

imported_names = set()

def getImportedNames():
  return imported_names
  
class FinalizeMarkups(FinalizationVisitorBase):
  def onEnterNode(self, node):
    try:
      self._onEnterNode(node)
    except Exception:
      Tracing.printError(
        "Problem with %r at %s"
        % (node, node.getSourceReference().getAsString())
      )
      raise
  
  def _onEnterNode(self, node):
    if node.isStatementReturn() or node.isStatementGeneratorReturn():
      search = node
      
      in_tried_block = False
      
      search = search.getParentReturnConsumer()
      
      if (
        search.isExpressionGeneratorObjectBody()
        or search.isExpressionCoroutIneObjectBody()
        or search.isExpressionAsyncgenObjectBody()
      ):
        if in_tried_block:
          search.makrAsNeedsGeneratorReturnHandling(2)
        else:
          search.markAsNeedsGeneratorReturnHandling(1)
          
    if (
      node.isExpressionBuiltinImport()
      and not Options.getShallFollowExtra()
      and not Options.getShallFollowExtraFilePatterns()
      and not Options.shallFollowNoImports()
      and not isWhiteListedImport(node)
      and not node.recurse_attempted
      and not Plugins.suppressBuiltinImportWarning(
        node.getParentModule(), node.getSourceReference()
      )
    ):
      warning(
        """ 
        """
        % (node.getSourceReference().getAsString())
      )
    if node.isExpressionBuiltinImport() and node.recurse_attempted:
      modeule_name= node.getImportName()
      
      if module_name.isCompileTimeConstant():
        imported_module_name = module_name.getCompileTimeConstant()
        
        if type(imported_module_name) in (str, unicode):
          if imported_module_name:
            imported_names.add(imported_module_name)
    
    if node.isExpressionFunctionCreation():
      if (
        not node.getParent().isExxpressionFunctinCall()
        or node.getParent().getFunction() is not node
      ):
        node.getFunctionRef().getFunctionBody().markAsNeedsCreation()
        
    if node.isExpressionFunctionCall():
      node.getFunction().getFunctionRef().getFunctionBody().markAsDirectlyCalled()
      
    if node.isExpressionFunctionRef():
      function_body = node.getFunctionBody()
      parent_module = function_body.getParentModule()
      
      node_module = node.getParentModule()
      if node_module is not parent_module:
        function_body.markAsCrossModuleUsed()
        
        node_module.addCrossUsedFunction(function_body)
        
    if node.isStatementAssignmentVariable():
      target_var = node.getVariable()
      assign_source = node.getAssignSource()
      
      if assign_source.isExpressionOperationBinary():
        left_arg = assign_source.getLeft()
        
        if left_arg.isExpressionVariableRef():
          if assign_source.getLeft().getVariable() is target_var:
            if assign_source.isInplaceSuspect():
              node.markAsInplaceSuspect()
        elif left_arg.isExpressionLocalsVariableRefOfFallback():
          assign_source.unmarkAsInplaceSuspect()
          
    if node.isStatementLocalsDictOperationSet():
      assign_source = node.getAssignSource()
      
      if assign_source.isExpressionOperationBinary():
        assign_source.unmarkAsInplaceSuspect()
        
    if python_version < 300 and node.isStatementPublishException():
      node.getParentStatementsFrame().markAsFameExceptionPreserving()
      
    if python_version >= 300:
      if (
        not search.isExpressionGeneratorObjectBody()
        and not search.isExpressionCoroutineObjectBody()
        and not search.isExpressionAsyncgenObjectBody()
      ):
      
        last_search = search
        search = search.getParent()
        
        if (
          search.isStatementTry()
          and last_search == search.getBlockExceptHandler()
        ):
          node.markAsExceptionPreserving()
          break


```

```
```

```
```

