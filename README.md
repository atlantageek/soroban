= Soroban

Soroban is a calculating engine that understands Excel formulas. Watch:

    > require 'soroban'
     => true 
    > s = Soroban::Sheet.()
     => #<Soroban::Sheet:0x10a374f28 @parser=#<SorobanParser:0x10a0b4838 @consume_all_input=true>> 
    > s.A1 = 2
     => 2 
    > s.A2 = 3
     => 3 
    > s.A3 = "=A1+A2^3"
     => "=A1+A2^3" 
    > s.A4 = "=IF(A3>100,'Large','Tiny')"
     => "=IF(A2>10,'Large','Tiny')" 
    > s.A4
     => "Tiny" 
    > s.A3
     => 29 
    > s.A2 = 5
     => 5 
    > s.A4
     => "Large" 

Soroban magically parses Excel formulas and rewrites them as valid Ruby code.
You can easily find out what it's done:

  > s.A3?
   => ...

== Formulas

Soroban formulas are strings that begin with the `=` symbol. It is therefore
easy to persist them, which is mighty handy if you need to parse an Excel
spreadsheet, rip out formulas, store everything to a database and then perform
calculations based on user input.

Soroban makes this easy, as it can tell you which cells you need to add to make
it possible to do the calculations you want, and it can iterate over all the
cells you've defined, so you can easily rip them out for persistence.

  > s.cells.map { |label, contents| "#{label}[#{contents}]" }
   => ...
  > s.A6 = "=A7+A8"
   => "=A7+A8"
  > s...

Note in the above that the iterator returns the label of the cell along with its
raw contents. If you want to iterate over cell values (including computed values
of formulas), then use `walk`.

== Cells

Cells have a label, like `B13`, which makes it easy to iterate them. It also
figures our cell labels when you assign arrays and hashes, like this:

  > s.B1 = [ 1, 2, 3, 4, 5 ]
   => ...
  > s.B3
   => 3
  > s.C1 = "=SUM(B1:B5)"
   => 16

To make parsing of an Excel file even easier, Soroban will let you know if
you've captured enough cells to be able to calculate the ones you've already
defined:

  > s.undefined.each { |label| }

== Iteration

You can walk over a range of cells.

  > s.walk('B1:C2').reduce(:+)

== Bindings

You can bind nice variable names to cells.

  > s.bind(:A1, :celcius)
   => 
  > s.bind(:A2, :faranheit)
   => 
  > s.bindings.each { |label, binding| "#{label}[#{binding}]" }

== Functions

Soroban implements some Excel functions, including `IF`, `SUM, `VLOOKUP`,
`HLOOKUP`, `MIN`, `MAX` and `AVERAGE`, but you may find that you need more than
those. In that case, it's easy to add more:

  > s.func_foo = lambda { |x, y| ... }
   => ...
  > s.A1 = "=FOO(A2, A3)"
   => ...

== Contributing to soroban
 
* Check out the latest master to make sure the feature hasn't been implemented or the bug hasn't been fixed yet.
* Check out the issue tracker to make sure someone already hasn't requested it and/or contributed it.
* Fork the project.
* Start a feature/bugfix branch.
* Commit and push until you are happy with your contribution.
* Make sure to add tests for it. This is important so I don't break it in a future version unintentionally.
* Please try not to mess with the Rakefile, version, or history. If you want to have your own version, or is otherwise necessary, that is fine, but please isolate to its own commit so I can cherry-pick around it.

== Copyright

Copyright (c) 2012 Agworld Pty. Ltd. See LICENSE.txt for further details.