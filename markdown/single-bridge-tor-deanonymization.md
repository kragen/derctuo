I just saw this:

    06:14 -!- rabbitear_g [~rabbitear@gateway/tor-sasl/rabbitearg/x-03735317] has quit [Remote host closed the 
              connection]
    06:14 -!- bb-8 [~bb-8@gateway/tor-sasl/bb-8] has quit [Read error: Connection reset by peer]
    06:14 -!- DiffieHellman [~Ident@gateway/tor-sasl/diffiehellman] has quit [Write error: Connection reset by 
              peer]
    06:14 -!- andreas303 [~andreas@gateway/tor-sasl/andreas303] has quit [Write error: Connection reset by peer]
    06:14 -!- stipa [~root@gateway/tor-sasl/stipa] has quit [Write error: Connection reset by peer]
    06:14 -!- Ryuuguu [~Ryuuguu@gateway/tor-sasl/ryuuguu] has quit [Remote host closed the connection]
    06:14 -!- martian67 [~martian67@about/linux/regular/martian67] has quit [Read error: Connection reset by peer]
    06:14 -!- ZombieChicken [~weechat@gateway/tor-sasl/forgottenwizard] has quit [Read error: Connection reset by
              peer]
    06:14 -!- CombatVet [~c4@gateway/tor-sasl/combatvet] has quit [Read error: Connection reset by peer]
    06:14 -!- milkt [~debian@gateway/tor-sasl/milkt] has quit [Read error: Connection reset by peer]
    06:14 -!- kreyren [~kreyren@fsf/member/kreyren] has quit [Read error: Connection reset by peer]
    06:14 -!- bamdad [~bamdad@gateway/tor-sasl/bamdad] has quit [Read error: Connection reset by peer]
    06:14 -!- bamdad [~bamdad@gateway/tor-sasl/bamdad] has joined ##electronics

13 users knocked off Freenode ##electronics at once, out of 630
people.  One of them, kreyren, was using a project hostname cloak.
Presumably a Tor node somewhere went down --- I think an exit node,
due to the ECONNRESET error message.  This event can be observed with
subsecond precision.

Suppose you wanted to deanonymize a Freenode Tor user.  You could set
up a bridge or a Tor entry node.  Periodically you could drop
connections from users who use it, a normal event that can be provoked
by backbone routing problems or Wi-Fi signal fades, after which the
user will retry connecting to Tor.  If you log the time of this event
while simultaneously observing Freenode, you can see if it correlates
with your target Freenode users going offline with a “Remote host
closed the connection” message.  If so, you log the IP address and
port.

These are relatively rare events; I observed one 8 minutes ago and
another 11 minutes ago in this same channel, giving a rate on the
order of 200 kiloseconds, so even a single “hit” is a p < .001% event
--- good enough, as they say, for government work.  Two hits on
different days would be a stronger confirmation and would also allow
you to characterize the Tor user’s IP address distribution.

An uncertainty that I need to test out is whether closing the circuit
from its origination point within the Tor network will immediately
close all the outgoing TCP connections from that circuit from the Tor
exit node, and if so, whether it’s a “connection closed” kind of
normal situation or more an RST RST RST kind of thing.

Another uncertainty is how many Tor entry nodes a given user will end
up using.  If they choose randomly with a nonzero probability for each
entry node, they will eventually use all of them, so every entry node
will have the opportunity to launch this attack.  Bridges, however, as
I understand the situation, are treated differently, and may be a
defense against the attack: each Tor bridge is only revealed to some
users, and the use of a Tor bridge would hide the real IP of the user
from the entry node.  So if you try to launch this attack with a
single bridge against a single user, you will fail with high
probability; if you try to launch it against many users, you will
succeed with only a few of them.

I’m not clear that this is something anybody needs to respond to or
defend against in any way, even if I’m correct, since Tor is not
designed or claimed to defend against a global passive adversary ---
that is a very difficult problem to solve.  And of course there are
some well-known problems with malicious exit nodes, and at least one
person has been prosecuted for sending a bomb threat to his university
over Tor, because he was the only person connecting to the Tor network
from the campus at the time the threat was sent.  But I’m surprised
that such a simple *active* attack seems so likely to work.
