---
layout: post
date: 2015-12-11 17:23:33 +0700
comments: true
categories: 
---


<div id="container">
    <div class="wrapper">
      <div id="mainCol">
        <h3 id="what-is-active-record-questionmark">1 What is Active Record?</h3><p>Active Record is the M in <a href="getting_started.html#the-mvc-architecture">MVC</a> - the
model - which is the layer of the system responsible for representing business
data and logic. Active Record facilitates the creation and use of business
objects whose data requires persistent storage to a database. It is an
implementation of the Active Record pattern which itself is a description of an
Object Relational Mapping system.</p><h4 id="the-active-record-pattern">1.1 The Active Record Pattern</h4><p><a href="http://www.martinfowler.com/eaaCatalog/activeRecord.html">Active Record was described by Martin Fowler</a>
in his book <em>Patterns of Enterprise Application Architecture</em>. In
Active Record, objects carry both persistent data and behavior which
operates on that data. Active Record takes the opinion that ensuring
data access logic as part of the object will educate users of that
object on how to write to and read from the database.</p><h4 id="object-relational-mapping">1.2 Object Relational Mapping</h4><p>Object-Relational Mapping, commonly referred to as its abbreviation ORM, is
a technique that connects the rich objects of an application to tables in
a relational database management system. Using ORM, the properties and
relationships of the objects in an application can be easily stored and
retrieved from a database without writing SQL statements directly and with less
overall database access code.</p><h4 id="active-record-as-an-orm-framework">1.3 Active Record as an ORM Framework</h4><p>Active Record gives us several mechanisms, the most important being the ability
to:</p>
<ul>
<li>Represent models and their data.</li>
<li>Represent associations between these models.</li>
<li>Represent inheritance hierarchies through related models.</li>
<li>Validate models before they get persisted to the database.</li>
<li>Perform database operations in an object-oriented fashion.</li>
</ul>
<h3 id="convention-over-configuration-in-active-record">2 Convention over Configuration in Active Record</h3><p>When writing applications using other programming languages or frameworks, it
may be necessary to write a lot of configuration code. This is particularly true
for ORM frameworks in general. However, if you follow the conventions adopted by
Rails, you'll need to write very little configuration (in some case no
configuration at all) when creating Active Record models. The idea is that if
you configure your applications in the very same way most of the time then this
should be the default way. Thus, explicit configuration would be needed
only in those cases where you can't follow the standard convention.</p><h4 id="naming-conventions">2.1 Naming Conventions</h4><p>By default, Active Record uses some naming conventions to find out how the
mapping between models and database tables should be created. Rails will
pluralize your class names to find the respective database table. So, for
a class <code>Book</code>, you should have a database table called <strong>books</strong>. The Rails
pluralization mechanisms are very powerful, being capable to pluralize (and
singularize) both regular and irregular words. When using class names composed
of two or more words, the model class name should follow the Ruby conventions,
using the CamelCase form, while the table name must contain the words separated
by underscores. Examples:</p>
<ul>
<li>Database Table - Plural with underscores separating words (e.g., <code>book_clubs</code>).</li>
<li>Model Class - Singular with the first letter of each word capitalized (e.g.,
<code>BookClub</code>).</li>
</ul>

<table class="responsive">
<thead>
<tr>
<th>Model / Class</th>
<th>Table / Schema</th>
</tr>
</thead>
<tbody>
<tr>
<td><code>Article</code></td>
<td><code>articles</code></td>
</tr>
<tr>
<td><code>LineItem</code></td>
<td><code>line_items</code></td>
</tr>
<tr>
<td><code>Deer</code></td>
<td><code>deers</code></td>
</tr>
<tr>
<td><code>Mouse</code></td>
<td><code>mice</code></td>
</tr>
<tr>
<td><code>Person</code></td>
<td><code>people</code></td>
</tr>
</tbody>
</table>
<h4 id="schema-conventions">2.2 Schema Conventions</h4><p>Active Record uses naming conventions for the columns in database tables,
depending on the purpose of these columns.</p>
<ul>
<li>
<strong>Foreign keys</strong> - These fields should be named following the pattern
<code>singularized_table_name_id</code> (e.g., <code>item_id</code>, <code>order_id</code>). These are the
fields that Active Record will look for when you create associations between
your models.</li>
<li>
<strong>Primary keys</strong> - By default, Active Record will use an integer column named
<code>id</code> as the table's primary key. When using <a href="migrations.html">Active Record
Migrations</a> to create your tables, this column will be
automatically created.</li>
</ul>
<p>There are also some optional column names that will add additional features
to Active Record instances:</p>
<ul>
<li>
<code>created_at</code> - Automatically gets set to the current date and time when the
record is first created.</li>
<li>
<code>updated_at</code> - Automatically gets set to the current date and time whenever
the record is updated.</li>
<li>
<code>lock_version</code> - Adds <a href="http://api.rubyonrails.org/classes/ActiveRecord/Locking.html">optimistic
locking</a> to
a model.</li>
<li>
<code>type</code> - Specifies that the model uses <a href="http://api.rubyonrails.org/classes/ActiveRecord/Base.html#class-ActiveRecord::Base-label-Single+table+inheritance">Single Table
Inheritance</a>.</li>
<li>
<code>(association_name)_type</code> - Stores the type for
<a href="association_basics.html#polymorphic-associations">polymorphic associations</a>.</li>
<li>
<code>(table_name)_count</code> - Used to cache the number of belonging objects on
associations. For example, a <code>comments_count</code> column in a <code>Articles</code> class that
has many instances of <code>Comment</code> will cache the number of existent comments
for each article.</li>
</ul>
<div class="note"><p>While these column names are optional, they are in fact reserved by Active Record. Steer clear of reserved keywords unless you want the extra functionality. For example, <code>type</code> is a reserved keyword used to designate a table using Single Table Inheritance (STI). If you are not using STI, try an analogous keyword like "context", that may still accurately describe the data you are modeling.</p></div><h3 id="creating-active-record-models">3 Creating Active Record Models</h3><p>It is very easy to create Active Record models. All you have to do is to
subclass the <code>ActiveRecord::Base</code> class and you're good to go:</p><div class="code_container">
<div><div id="highlighter_867243" class="syntaxhighlighter nogutter  ruby"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="ruby keyword">class</code> <code class="ruby plain">Product &lt; ActiveRecord::Base</code></div><div class="line number2 index1 alt1"><code class="ruby keyword">end</code></div></div></td></tr></tbody></table></div></div>
</div>
<p>This will create a <code>Product</code> model, mapped to a <code>products</code> table at the
database. By doing this you'll also have the ability to map the columns of each
row in that table with the attributes of the instances of your model. Suppose
that the <code>products</code> table was created using an SQL sentence like:</p><div class="code_container">
<div><div id="highlighter_272185" class="syntaxhighlighter nogutter  sql"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="sql keyword">CREATE</code> <code class="sql keyword">TABLE</code> <code class="sql plain">products (</code></div><div class="line number2 index1 alt1"><code class="sql spaces">&nbsp;&nbsp;&nbsp;</code><code class="sql plain">id </code><code class="sql keyword">int</code><code class="sql plain">(11) </code><code class="sql color1">NOT</code> <code class="sql color1">NULL</code> <code class="sql plain">auto_increment,</code></div><div class="line number3 index2 alt2"><code class="sql spaces">&nbsp;&nbsp;&nbsp;</code><code class="sql keyword">name</code> <code class="sql keyword">varchar</code><code class="sql plain">(255),</code></div><div class="line number4 index3 alt1"><code class="sql spaces">&nbsp;&nbsp;&nbsp;</code><code class="sql keyword">PRIMARY</code> <code class="sql keyword">KEY</code>&nbsp; <code class="sql plain">(id)</code></div><div class="line number5 index4 alt2"><code class="sql plain">);</code></div></div></td></tr></tbody></table></div></div>
</div>
<p>Following the table schema above, you would be able to write code like the
following:</p><div class="code_container">
<div><div id="highlighter_482088" class="syntaxhighlighter nogutter  ruby"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="ruby plain">p = Product.</code><code class="ruby keyword">new</code></div><div class="line number2 index1 alt1"><code class="ruby plain">p.name = </code><code class="ruby string">"Some Book"</code></div><div class="line number3 index2 alt2"><code class="ruby plain">puts p.name </code><code class="ruby comments"># "Some Book"</code></div></div></td></tr></tbody></table></div></div>
</div>
<h3 id="overriding-the-naming-conventions">4 Overriding the Naming Conventions</h3><p>What if you need to follow a different naming convention or need to use your
Rails application with a legacy database? No problem, you can easily override
the default conventions.</p><p>You can use the <code>ActiveRecord::Base.table_name=</code> method to specify the table
name that should be used:</p><div class="code_container">
<div><div id="highlighter_88734" class="syntaxhighlighter nogutter  ruby"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="ruby keyword">class</code> <code class="ruby plain">Product &lt; ActiveRecord::Base</code></div><div class="line number2 index1 alt1"><code class="ruby spaces">&nbsp;&nbsp;</code><code class="ruby keyword">self</code><code class="ruby plain">.table_name = </code><code class="ruby string">"my_products"</code></div><div class="line number3 index2 alt2"><code class="ruby keyword">end</code></div></div></td></tr></tbody></table></div></div>
</div>
<p>If you do so, you will have to define manually the class name that is hosting
the fixtures (my_products.yml) using the <code>set_fixture_class</code> method in your test
definition:</p><div class="code_container">
<div><div id="highlighter_931985" class="syntaxhighlighter nogutter  ruby"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="ruby keyword">class</code> <code class="ruby plain">ProductTest &lt; ActiveSupport::TestCase</code></div><div class="line number2 index1 alt1"><code class="ruby spaces">&nbsp;&nbsp;</code><code class="ruby plain">set_fixture_class my_products: Product</code></div><div class="line number3 index2 alt2"><code class="ruby spaces">&nbsp;&nbsp;</code><code class="ruby plain">fixtures </code><code class="ruby color2">:my_products</code></div><div class="line number4 index3 alt1"><code class="ruby spaces">&nbsp;&nbsp;</code><code class="ruby plain">...</code></div><div class="line number5 index4 alt2"><code class="ruby keyword">end</code></div></div></td></tr></tbody></table></div></div>
</div>
<p>It's also possible to override the column that should be used as the table's
primary key using the <code>ActiveRecord::Base.primary_key=</code> method:</p><div class="code_container">
<div><div id="highlighter_557431" class="syntaxhighlighter nogutter  ruby"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="ruby keyword">class</code> <code class="ruby plain">Product &lt; ActiveRecord::Base</code></div><div class="line number2 index1 alt1"><code class="ruby spaces">&nbsp;&nbsp;</code><code class="ruby keyword">self</code><code class="ruby plain">.primary_key = </code><code class="ruby string">"product_id"</code></div><div class="line number3 index2 alt2"><code class="ruby keyword">end</code></div></div></td></tr></tbody></table></div></div>
</div>
<h3 id="crud-reading-and-writing-data">5 CRUD: Reading and Writing Data</h3><p>CRUD is an acronym for the four verbs we use to operate on data: <strong>C</strong>reate,
<strong>R</strong>ead, <strong>U</strong>pdate and <strong>D</strong>elete. Active Record automatically creates methods
to allow an application to read and manipulate data stored within its tables.</p><h4 id="create">5.1 Create</h4><p>Active Record objects can be created from a hash, a block or have their
attributes manually set after creation. The <code>new</code> method will return a new
object while <code>create</code> will return the object and save it to the database.</p><p>For example, given a model <code>User</code> with attributes of <code>name</code> and <code>occupation</code>,
the <code>create</code> method call will create and save a new record into the database:</p><div class="code_container">
<div><div id="highlighter_517785" class="syntaxhighlighter nogutter  ruby"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="ruby plain">user = User.create(name: </code><code class="ruby string">"David"</code><code class="ruby plain">, occupation: </code><code class="ruby string">"Code Artist"</code><code class="ruby plain">)</code></div></div></td></tr></tbody></table></div></div>
</div>
<p>Using the <code>new</code> method, an object can be instantiated without being saved:</p><div class="code_container">
<div><div id="highlighter_663466" class="syntaxhighlighter nogutter  ruby"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="ruby plain">user = User.</code><code class="ruby keyword">new</code></div><div class="line number2 index1 alt1"><code class="ruby plain">user.name = </code><code class="ruby string">"David"</code></div><div class="line number3 index2 alt2"><code class="ruby plain">user.occupation = </code><code class="ruby string">"Code Artist"</code></div></div></td></tr></tbody></table></div></div>
</div>
<p>A call to <code>user.save</code> will commit the record to the database.</p><p>Finally, if a block is provided, both <code>create</code> and <code>new</code> will yield the new
object to that block for initialization:</p><div class="code_container">
<div><div id="highlighter_184685" class="syntaxhighlighter nogutter  ruby"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="ruby plain">user = User.</code><code class="ruby keyword">new</code> <code class="ruby keyword">do</code> <code class="ruby plain">|u|</code></div><div class="line number2 index1 alt1"><code class="ruby spaces">&nbsp;&nbsp;</code><code class="ruby plain">u.name = </code><code class="ruby string">"David"</code></div><div class="line number3 index2 alt2"><code class="ruby spaces">&nbsp;&nbsp;</code><code class="ruby plain">u.occupation = </code><code class="ruby string">"Code Artist"</code></div><div class="line number4 index3 alt1"><code class="ruby keyword">end</code></div></div></td></tr></tbody></table></div></div>
</div>
<h4 id="read">5.2 Read</h4><p>Active Record provides a rich API for accessing data within a database. Below
are a few examples of different data access methods provided by Active Record.</p><div class="code_container">
<div><div id="highlighter_911082" class="syntaxhighlighter nogutter  ruby"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="ruby comments"># return a collection with all users</code></div><div class="line number2 index1 alt1"><code class="ruby plain">users = User.all</code></div></div></td></tr></tbody></table></div></div>
</div>
<div class="code_container">
<div><div id="highlighter_674950" class="syntaxhighlighter nogutter  ruby"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="ruby comments"># return the first user</code></div><div class="line number2 index1 alt1"><code class="ruby plain">user = User.first</code></div></div></td></tr></tbody></table></div></div>
</div>
<div class="code_container">
<div><div id="highlighter_455212" class="syntaxhighlighter nogutter  ruby"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="ruby comments"># return the first user named David</code></div><div class="line number2 index1 alt1"><code class="ruby plain">david = User.find_by(name: </code><code class="ruby string">'David'</code><code class="ruby plain">)</code></div></div></td></tr></tbody></table></div></div>
</div>
<div class="code_container">
<div><div id="highlighter_445213" class="syntaxhighlighter nogutter  ruby"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="ruby comments"># find all users named David who are Code Artists and sort by created_at in reverse chronological order</code></div><div class="line number2 index1 alt1"><code class="ruby plain">users = User.where(name: </code><code class="ruby string">'David'</code><code class="ruby plain">, occupation: </code><code class="ruby string">'Code Artist'</code><code class="ruby plain">).order(created_at: </code><code class="ruby color2">:desc</code><code class="ruby plain">)</code></div></div></td></tr></tbody></table></div></div>
</div>
<p>You can learn more about querying an Active Record model in the <a href="active_record_querying.html">Active Record
Query Interface</a> guide.</p><h4 id="update">5.3 Update</h4><p>Once an Active Record object has been retrieved, its attributes can be modified
and it can be saved to the database.</p><div class="code_container">
<div><div id="highlighter_183802" class="syntaxhighlighter nogutter  ruby"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="ruby plain">user = User.find_by(name: </code><code class="ruby string">'David'</code><code class="ruby plain">)</code></div><div class="line number2 index1 alt1"><code class="ruby plain">user.name = </code><code class="ruby string">'Dave'</code></div><div class="line number3 index2 alt2"><code class="ruby plain">user.save</code></div></div></td></tr></tbody></table></div></div>
</div>
<p>A shorthand for this is to use a hash mapping attribute names to the desired
value, like so:</p><div class="code_container">
<div><div id="highlighter_894317" class="syntaxhighlighter nogutter  ruby"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="ruby plain">user = User.find_by(name: </code><code class="ruby string">'David'</code><code class="ruby plain">)</code></div><div class="line number2 index1 alt1"><code class="ruby plain">user.update(name: </code><code class="ruby string">'Dave'</code><code class="ruby plain">)</code></div></div></td></tr></tbody></table></div></div>
</div>
<p>This is most useful when updating several attributes at once. If, on the other
hand, you'd like to update several records in bulk, you may find the
<code>update_all</code> class method useful:</p><div class="code_container">
<div><div id="highlighter_322260" class="syntaxhighlighter nogutter  ruby"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="ruby plain">User.update_all </code><code class="ruby string">"max_login_attempts = 3, must_change_password = 'true'"</code></div></div></td></tr></tbody></table></div></div>
</div>
<h4 id="delete">5.4 Delete</h4><p>Likewise, once retrieved an Active Record object can be destroyed which removes
it from the database.</p><div class="code_container">
<div><div id="highlighter_420136" class="syntaxhighlighter nogutter  ruby"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="ruby plain">user = User.find_by(name: </code><code class="ruby string">'David'</code><code class="ruby plain">)</code></div><div class="line number2 index1 alt1"><code class="ruby plain">user.destroy</code></div></div></td></tr></tbody></table></div></div>
</div>
<h3 id="validations">6 Validations</h3><p>Active Record allows you to validate the state of a model before it gets written
into the database. There are several methods that you can use to check your
models and validate that an attribute value is not empty, is unique and not
already in the database, follows a specific format and many more.</p><p>Validation is a very important issue to consider when persisting to the database, so
the methods <code>save</code> and <code>update</code> take it into account when
running: they return <code>false</code> when validation fails and they didn't actually
perform any operation on the database. All of these have a bang counterpart (that
is, <code>save!</code> and <code>update!</code>), which are stricter in that
they raise the exception <code>ActiveRecord::RecordInvalid</code> if validation fails.
A quick example to illustrate:</p><div class="code_container">
<div><div id="highlighter_131697" class="syntaxhighlighter nogutter  ruby"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="ruby keyword">class</code> <code class="ruby plain">User &lt; ActiveRecord::Base</code></div><div class="line number2 index1 alt1"><code class="ruby spaces">&nbsp;&nbsp;</code><code class="ruby plain">validates </code><code class="ruby color2">:name</code><code class="ruby plain">, presence: </code><code class="ruby keyword">true</code></div><div class="line number3 index2 alt2"><code class="ruby keyword">end</code></div><div class="line number4 index3 alt1">&nbsp;</div><div class="line number5 index4 alt2"><code class="ruby plain">user = User.</code><code class="ruby keyword">new</code></div><div class="line number6 index5 alt1"><code class="ruby plain">user.save&nbsp; </code><code class="ruby comments"># =&gt; false</code></div><div class="line number7 index6 alt2"><code class="ruby plain">user.save! </code><code class="ruby comments"># =&gt; ActiveRecord::RecordInvalid: Validation failed: Name can't be blank</code></div></div></td></tr></tbody></table></div></div>
</div>
<p>You can learn more about validations in the <a href="active_record_validations.html">Active Record Validations
guide</a>.</p><h3 id="callbacks">7 Callbacks</h3><p>Active Record callbacks allow you to attach code to certain events in the
life-cycle of your models. This enables you to add behavior to your models by
transparently executing code when those events occur, like when you create a new
record, update it, destroy it and so on. You can learn more about callbacks in
the <a href="active_record_callbacks.html">Active Record Callbacks guide</a>.</p><h3 id="migrations">8 Migrations</h3><p>Rails provides a domain-specific language for managing a database schema called
migrations. Migrations are stored in files which are executed against any
database that Active Record supports using <code>rake</code>. Here's a migration that
creates a table:</p><div class="code_container">
<div><div id="highlighter_366383" class="syntaxhighlighter nogutter  ruby"><table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="code"><div class="container"><div class="line number1 index0 alt2"><code class="ruby keyword">class</code> <code class="ruby plain">CreatePublications &lt; ActiveRecord::Migration</code></div><div class="line number2 index1 alt1"><code class="ruby spaces">&nbsp;&nbsp;</code><code class="ruby keyword">def</code> <code class="ruby plain">change</code></div><div class="line number3 index2 alt2"><code class="ruby spaces">&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="ruby plain">create_table </code><code class="ruby color2">:publications</code> <code class="ruby keyword">do</code> <code class="ruby plain">|t|</code></div><div class="line number4 index3 alt1"><code class="ruby spaces">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="ruby plain">t.string </code><code class="ruby color2">:title</code></div><div class="line number5 index4 alt2"><code class="ruby spaces">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="ruby plain">t.text </code><code class="ruby color2">:description</code></div><div class="line number6 index5 alt1"><code class="ruby spaces">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="ruby plain">t.references </code><code class="ruby color2">:publication_type</code></div><div class="line number7 index6 alt2"><code class="ruby spaces">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="ruby plain">t.integer </code><code class="ruby color2">:publisher_id</code></div><div class="line number8 index7 alt1"><code class="ruby spaces">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="ruby plain">t.string </code><code class="ruby color2">:publisher_type</code></div><div class="line number9 index8 alt2"><code class="ruby spaces">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="ruby plain">t.boolean </code><code class="ruby color2">:single_issue</code></div><div class="line number10 index9 alt1">&nbsp;</div><div class="line number11 index10 alt2"><code class="ruby spaces">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="ruby plain">t.timestamps null: </code><code class="ruby keyword">false</code></div><div class="line number12 index11 alt1"><code class="ruby spaces">&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="ruby keyword">end</code></div><div class="line number13 index12 alt2"><code class="ruby spaces">&nbsp;&nbsp;&nbsp;&nbsp;</code><code class="ruby plain">add_index </code><code class="ruby color2">:publications</code><code class="ruby plain">, </code><code class="ruby color2">:publication_type_id</code></div><div class="line number14 index13 alt1"><code class="ruby spaces">&nbsp;&nbsp;</code><code class="ruby keyword">end</code></div><div class="line number15 index14 alt2"><code class="ruby keyword">end</code></div></div></td></tr></tbody></table></div></div>
</div>
<p>Rails keeps track of which files have been committed to the database and
provides rollback features. To actually create the table, you'd run <code>rake db:migrate</code>
and to roll it back, <code>rake db:rollback</code>.</p><p>Note that the above code is database-agnostic: it will run in MySQL,
PostgreSQL, Oracle and others. You can learn more about migrations in the
<a href="migrations.html">Active Record Migrations guide</a>.</p>

      </div>
    </div>
  </div>