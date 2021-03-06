

## Service parameters

Backup count 

partition count

Serializer

PAS

KeyAssociation





## Kick Test

Pull the network cable

## Monitoring

Service avg transaction time



## best practices - Production recommendations

Name your cluster
	avoid promiscuity due to multicast
	name: Location-env-secretKeyWord
	
Set mode
	mode=dev
	mode=prod
	avoids joining cluster
	
Set initial and max JVM sizes to allocate all physical memory upfront
	example: -Xms3G –Xmx3G
	No nasty surprises as data volumes grow and physical memory becomes exhausted.
	
On OOM JVM behaviour is unpredictable and may endanger the cluster once OOM has occurred.
	Unix
	-XX:OnOutOfMemoryError="kill -9 %p"
	Windows
	-XX:OnOutOfMemoryError="taskkill /F /PID %p"
	
	
Heap dump on OOM Heap dumps assist your diagnosis:
	-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=<node-specific-file>
	
Don't block on BackingMapListener because sync on underlying map

CacheStore just consume a thread but does not block on the underlying map

Concurrent Mark Sweep
	Short full GC times for relatively little overhead
	-XX:+UseConcMarkSweepGC
	-XX:CMSInitiatingOccupancyFraction=75
	-XX:+UseInitiatingOccupancyOnly
	
	Monitor how often GC
	Monitor how low the memory gets after GC. It tells you how much memory you really use
	Make sure you don't get to the threshold
	
	
Log Garbage Collections
	-Xloggc:<node-specific-file> -XX:+PrintGCDateStamps
	For more verbose logging:
	-XX:+PrintGCDetails -XX:+PrintTenuringDistribution
	
	
	
## How much uncommitted physical memory do you need on a server?
• Swapping is extremely bad news.
• The most likely time to notice pages
• swapped out is during GC
• Some UNIX sysadmins standardise backup or monitoring tools that gobble up huge amounts of memory when they run.

• Can we prevent swapping?
• Leave lots of OS memory uncommitted. How much?
• Swappiness
• Huge pages
• sudo swapoff –a
• mlockall


RAM Pinning with mlockall • mlockall : standard C library function
• Locks a process’s virtual address space into physical memory
• Requires the memlock user limit to be set in /etc/security/limits.conf
• Can be called via JNA


    
    
RAM Pinning with mlockall package com.csg.dtacc.coherence.utils;
	import com.sun.jna.Library; import com.sun.jna.Native;
	public class MemLock {
		public static final int MCL_CURRENT = 1; public static final int MCL_FUTURE = 2;
		private interface CLibrary extends Library { int mlockall(int flags);
		}
		private synchronized static void mlockall() {
			CLibrary instance = (CLibrary) Native.loadLibrary("c", CLibrary.class); return instance.mlockall(MCL_CURRENT | MCL_FUTURE);
		}
	}
	
	
Test Pof Fidelity
	private void assertPofFidelity(Object example) {
		ConfigurablePofContext cxt = new ConfigurablePofContext(POF_CONFIG_XML);
		Binary binary = ExternalizableHelper.toBinary(example, cxt); Object result = ExternalizableHelper.fromBinary(binary, cxt);
		assertEquals(example, result);
		// or
		// assertTrue(EqualsBuilder.reflectionEquals(example, result)); // apache common-lang convenience
		}
		
		
## What happens when an Exception is thrown?

### in entryProcessor.process()

    after  3.7.1.0
    it rolls back
    and get PortableException or WrapperException
    getcause() will contain the exception it is extending PortableException otherwise you get a generic exception
    
    !! 3.7.1.0 it goes to the backup node !!  (bug)
    
 in entryProcessor.process()
Map result = cache.invokeAll(keyset, entryProcessor);
rollback partition and wont execute partition that have not finished

in filter.evaluate()
  Set result = cache.entrySet(filter);
in entryProcessor.process() or filter.evaluate()
    Map result = cache.invokeAll(filter, entryProcessor);
    it's undeterministic

    
    
## Write-behind

### threading

### remove does not write behind


## Coherence Key Howto

### equals and hashCode need to be implemented correctly for any type used as a key

### All serializable (non transient) fields in the key class must be used in the equals implementation

See [this blog](http://blackbeanbag.net/wp/2010/06/06/coherence-key-howto/) and [this blog](http://codefhtagn.blogspot.co.uk/2010/09/coherence-when-key-is-not-whole-key.html)

## 21 March 2013 Coherence SIG
new version 3.7.1.8

cohrence.oracle.com/display/CSIG

### Elastic Data

Local cAche
    in memory binary map
    eviction/expiry support
    
External Scheme
    BDB
    NIO
    LH (avoid its use!)
    
Local Cache limitations:
    allocation made on heap
    high storage in backing map == large heap
    unable to isolate
    
Elastic Data objectives
    both ram and disk
    retain keys in memory
    journaling - append aonly
    Overflow from RAM to disk
    big data - 
    
History
    introduced in 3.7
    3.7.1 exalogic perormance enhancements
    12.1.2 minor changes
    
