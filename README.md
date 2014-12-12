IntegerPartitionClass
=====================

This is a simple class for generating random integer partitions under various restrictions.

Download the file and put it in the same folder as your main.cpp file.  Then the following code should compile

// main.cpp 
 #include <iostream>
 #include "IntegerPartition.h"
 
 using std::cout;
 using std::endl;
 typedef unsigned long long ull;
 
 int main(int argc, const char * argv[]) {
 
 // create an unsigned long long variable n
 ull n = 100;
 
 // Create an unrestricted integer partition
 IP::UnrestrictedPartition ip;
 
 // Randomize the partition, each partition is equally likely to be generated.
 ip(n);
 
 // You can print out the parts of the partition easily
 cout << ip << endl;
 
 // A more visual representation of the partition.
 ip.Ferrer();
 
 // Convert to a multiset to access parts more directly
 auto parts = ip.AsMultiset();
 
 for(auto x : parts) cout << x << ",";
 cout << endl;
 
 // It is slightly faster to randomize when we allow the size to also be random
 ip.RandomSize(n);
 
 // We can check on the new size using the n() function
 cout << "Partition: " << ip << " \nhas size "<< ip.n() << " <-- probably not exactly "<< n <<endl;
 
 // A rejection sampling algorithm that maintains size
 ip.RejectionSampling(n);
 
 // We can check on the new size using the n() function, this one should be exactly n
 cout << "Partition: " << ip << " \nhas size "<< ip.n() << " <-- should be exactly "<<n<<endl;
 
 // A Probabilistic divide-and-conquer algorithm that maintains size is more efficient
 ip.PDCDeterministicSecondHalf(n);
 
 // We can check on the new size using the n() function, this one should be exactly n
 cout << "Partition: " << ip << " \nhas size "<< ip.n() << " <-- should be exactly "<<n<< endl;
 
 // This operator will always return a random partition of the exact size of its argument.
 // Right now it calls PDCDeterministicSecondHalf, but this will be swapped out by more efficient algorithms
 // as they become available.  This should be the default choice as it will be robust with respect to updates.
 ip(n);
 
 // We can check on the new size using the n() function, this one should be exactly n
 cout << "Partition: " << ip << " \nhas size "<< ip.n() << " <-- should be exactly "<<n<< endl;
 
 // Next we can introduce restrictions.  This one enforces all parts to be even.
 IP::EvenPartition even;
 
 // Randomize, maintain exact size n
 even(n);
 
 // We can check on the new size using the n() function, this one should be exactly n
 cout << "Partition into even parts: " << even << " \nhas size "<< even.n() << " <-- should be exactly "<<n<< endl;
 
 // This one enforces all parts to be odd.
 IP::OddPartition odd;
 
 // Randomize, maintain exact size n
 odd(n);
 
 // We can check on the new size using the n() function, this one should be exactly n
 cout << "Partition into odd parts: " << odd << " \nhas size "<< odd.n() << " <-- should be exactly "<<n<< endl;
 
 
 // We can even create our own restrictions.  It is important that u(1), u(2), ..., is an INCREASING sequence.
 // For example, let's consider partitions into perfect cubes.
 // {1^3, 2^3, 3^3, ... }
 struct PerfectCubes { ull operator()(ull i) { return i*i*i; } };
 
 IP::IntegerPartition< PerfectCubes > cubes;
 cubes(n);
 cout << "Partition into cubes: " << cubes << " \nhas size "<< cubes.n() << " <-- should be exactly "<<n<< endl;
 
 // We can also make the set of allowable part sizes finite by returning 0 otherwise.
 // Here we consider the set of partitions with parts all <= 10.
 
 struct MaxPartSize { ull operator()(ull i) { return i<=10 ? i : 0; } };
 
 IP::IntegerPartition< MaxPartSize > parts_less_10;
 parts_less_10(n);
 cout << "Partition into parts less than 10: " << parts_less_10 << " \nhas size "<< parts_less_10.n() << " <-- should be exactly "<<n<< endl;
 
 
 // Here we consider the set of partitions with all parts >= 4
 struct MinPartSize { ull operator()(ull i) { return i+3; } };
 IP::IntegerPartition< MinPartSize > parts_greater_4;
 parts_greater_4(n);
 cout << "Partition into parts at least 4: " << parts_greater_4 << " \nhas size "<< parts_greater_4.n() << " <-- should be exactly "<<n<< endl;
 
 // We can do partitions into parts of size J mod M.  Because it is templated the struct cannot be local.
 IP::IntegerPartition< IP::JmodM<ull, 5, 7> > mod_ip;
 mod_ip(n);
 cout << "Partition into parts = 5 mod 7: " << mod_ip << " \nhas size "<< mod_ip.n() << " <-- should be exactly "<<n<< endl;
 
 return 0;
 }

Then the output should look something like:

17,7,4,4,4,4,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 *
 * * * *
 * * * *
 * * * *
 * * * *
 * * * * * * *
 * * * * * * * * * * * * * * * * *
 17,7,4,4,4,4,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,
 Partition: 29,15,11,8,7,6,5,4,4,4,3,3,3,3,3,3,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,2,1,1,1,1,1,1,1,1,1,1,1,
 has size 154 <-- probably not exactly 100
 Partition: 39,13,7,6,6,4,3,3,3,3,3,3,3,2,2,
 has size 100 <-- should be exactly 100
 Partition: 45,13,13,9,6,6,3,3,2,
 has size 100 <-- should be exactly 100
 Partition: 23,18,17,6,5,4,4,4,2,2,2,2,2,1,1,1,1,1,1,1,1,1,
 has size 100 <-- should be exactly 100
 Partition into even parts: 28,12,10,6,6,6,4,4,4,4,4,4,4,4,
 has size 100 <-- should be exactly 100
 Partition into odd parts: 17,17,15,15,7,7,5,5,5,3,1,1,1,1,
 has size 100 <-- should be exactly 100
 Partition into cubes: 8,8,8,8,8,8,8,8,8,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,
 has size 100 <-- should be exactly 100
 Partition into parts less than 10: 10,9,8,8,8,7,7,5,4,4,2,2,2,2,2,2,2,2,2,2,2,2,2,2,1,1,
 has size 100 <-- should be exactly 100
 Partition into parts at least 4: 32,21,13,13,11,5,5,
 has size 100 <-- should be exactly 100
 Partition into parts = 5 mod 7: 19,19,19,19,12,12,
 has size 100 <-- should be exactly 100


