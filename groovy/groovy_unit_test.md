# Unit Testing and Mocking

### Stubs vs. Mocks


### Mocking Using Categories


```
import com.agiledeveloper.CodeWithHeavierDependencies


```

### Mocking Using ExpandoMetaClass

```

class TestUsingExpandoMetaClass extends GroovyTestCase {


void testMyMethod() {
	emc.println = { text -> result = text }
	testObj.metaClass = emc

```
class TestUsingMap extends GroovyTestCase { void testMethodA() {
```


### Mocking Using the Groovy Mock Library
#### Using StubFor
```
import com.agiledeveloper.ClassWithDependency
	 fileMock.demand.write { text = it.toString() }
```


#### Using MockFor


```
class TwoFileUserTest extends GroovyTestCase { 
	void testUseFiles() {
		fileMock.demand.write() 	{ assertEquals testData, it } 
		fileMock.demand.write() { assertEquals testData.size(), it }
		fileMock.demand.close(2..2) {}

	void tearDown() {



```