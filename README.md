# AI-Zen
### AI Zen is a virtual tier 1 support agent at the cable company Livewire.


#### Server OS/applications used:

* Debian 12
* Perl
* NGINX
* Signalwire API's


## Functions

Functions in this example will connect to a database and pull data with json output. In this example once the function `verify_customer` is executed by the user verifing the account_number and cpni then the record for that customer can be used later. The prompt for this example won't allow any further steps to happen unless verify_customer is validated.


verify_customer
-----------------------

* Name:`verify_customer`
* Purpose:Verify customer account number, cpni first, last name and phone number
* Argument:account_number|7 digit number,cpni|4 digit number

```perl

my $env       = shift;
my $req       = Plack::Request->new( $env );
my $swml      = SignalWire::ML->new;
my $post_data = decode_json( $req->raw_body );
my $data      = $post_data->{argument}->{parsed}->[0];
my $res       = Plack::Response->new( 200 );
my $agent     = $req->param( 'agent_id' );

print STDERR Dumper $data;
print STDERR $agent;

broadcast_by_agent_id( $agent, $data );

my $dbh = DBI->connect(
  "dbi:Pg:dbname=$database;host=$host;port=$port",
  $dbusername,
  $dbpassword,
  { AutoCommit => 1, RaiseError => 1 } ) or die "Couldn't execute statement: $DBI::errstr\n";
my $sql = "SELECT * FROM customers WHERE account_number = ? AND cpni = ? LIMIT 1";

my $sth = $dbh->prepare( $sql );
$sth->bind_param(1,$data->{account_number});
$sth->bind_param(2,$data->{cpni});
$sth->execute() or die "Couldn't execute statement: $DBI::errstr";

my $agents = $sth->fetchrow_hashref;

broadcast_by_agent_id( $agent, $agents );

if ($data->{account_number} eq $agents->{account_number} && $data->{cpni} eq $agents->{cpni}) {
    $res->body( $swml->swaig_response_json( { response => "Account verified, proceed", action => [  { set_meta_data => { customer => $agents } } ] } ) );
    broadcast_by_agent_id( $agent, "valid" );
    broadcast_by_agent_id( $agent,  $swml->swaig_response_json( { response => "Account verified, proceed", action => [  { set_meta_data => { customer => $agents } } ] } ) );
} else {
    # This is the failure
    $res->body( $swml->swaig_response_json( { response => "Account invalid try again" } ) ) ;
    broadcast_by_agent_id( $agent, "invalid" );
    broadcast_by_agent_id( $agent, $swml->swaig_response_json( { response => "Account invalid try again" } ) );
}

return $res->finalize;
```

## Meta_Data Functions

Meta_Data Functions access the output of an already executed function. In this example once the function verify_customer is executed by the user by verifing account_number and cpni then the record for that customer is accessable via json for any meta_data_functions.

modem_swap
-----------------------

* Name:`modem_swap`
* Purpose:Swap the users modem
* Argument:mac_address|new modem MAC Address in lowercase hex 12 characters

```perl

my $env       = shift;
my $req       = Plack::Request->new( $env );
my $swml      = SignalWire::ML->new;
my $post_data = decode_json( $req->raw_body );
my $data      = $post_data->{argument}->{parsed}->[0];
my $res       = Plack::Response->new( 200 );
my $agent     = $req->param( 'agent_id' );
my $customer  = $post_data->{meta_data}->{customer};

broadcast_by_agent_id( $agent, $data );
broadcast_by_agent_id( $agent, $customer );

sub is_valid_mac_address {
    my ($mac) = @_;

    # Regular expression for a MAC address with optional colons or dashes
    my $mac_regex = qr/^([0-9A-Fa-f]{2}(:|-)?){5}[0-9A-Fa-f]{2}$/;

    return $mac =~ $mac_regex;
}

# Example

my $dbh = DBI->connect(
  "dbi:Pg:dbname=$database;host=$host;port=$port",
  $dbusername,
  $dbpassword,
  { AutoCommit => 1, RaiseError => 1 } ) or die "Couldn't execute statement: $DBI::errstr\n";

if ( is_valid_mac_address($data->{mac_address}) ) {
# Add a SQL update statement (modify this to match your actual table and field names)
    my $update_sql = "UPDATE customers SET mac_address = ? WHERE account_number = ?";
    my $update_sth = $dbh->prepare( $update_sql );
    $update_sth->bind_param(1, $data->{mac_address});
    $update_sth->bind_param(2, $customer->{account_number});
    $update_sth->execute() or die "Couldn't execute statement: $DBI::errstr";

    $update_sth->finish;

# Your existing SELECT query
    my $select_sql = "SELECT * FROM customers WHERE account_number = ? LIMIT 1";
    my $select_sth = $dbh->prepare( $select_sql );
    $select_sth->bind_param(1, $customer->{account_number} );
    $select_sth->execute() or die "Couldn't execute statement: $DBI::errstr";

    my $agents = $select_sth->fetchrow_hashref;

    $select_sth->finish;

    if (lc $agents->{mac_address} eq lc $data->{mac_address} ) {
        $res->body( $swml->swaig_response_json( { response => "Customers modem mac address updated, please allow 5 minutes for all systems to update and plug in your new modem.", action => [  { set_meta_data => { customer => $agents } } ] } ) );
        broadcast_by_agent_id( $agent, $agents );
        
    } else {
        $res->body( $swml->swaig_response_json( { response => "Error swapping modem, mac address may be invalid, try again.  #1" } ) );
    }
} else {
    $res->body( $swml->swaig_response_json( { response => "Error swapping modem, mac address may be invalid, try again later.  #2" } ) );  
}

return $res->finalize;

```

speed_test
-----------------------

* Name:`speed_test`
* Purpose:Test upload and download speed from the modem
* Argument:account_number|7 digit number,cpni|4 digit number

```perl

my $env       = shift;
my $req       = Plack::Request->new( $env );
my $swml      = SignalWire::ML->new;
my $post_data = decode_json( $req->raw_body );
my $data      = $post_data->{argument}->{parsed}->[0];
my $res       = Plack::Response->new( 200 );
my $agent     = $req->param( 'agent_id' );
#Use this as a seconday function once primary function is used from the database
my $customer  = $post_data->{meta_data}->{customer};

print STDERR Dumper $data;
print STDERR $agent;

broadcast_by_agent_id( $agent, $customer );

#my $dbh = DBI->connect(
#  "dbi:Pg:dbname=$database;host=$host;port=$port",
#  $dbusername,
#  $dbpassword,
#  { AutoCommit => 1, RaiseError => 1 } ) or die "Couldn't execute statement: $DBI::errstr\n";

# Update the SQL query to include new conditions
#my $sql = "SELECT * FROM customers WHERE modem_speed_upload = ? AND modem_speed_download = ? LIMIT 1";

#my $sth = $dbh->prepare( $sql );
#$sth->bind_param(1, $data->{modem_speed_upload});
#$sth->bind_param(2, $data->{modem_speed_download});
#$sth->execute() or die "Couldn't execute statement: $DBI::errstr";

#my $agents = $sth->fetchrow_hashref;

if ($customer->{modem_speed_upload} && $customer->{modem_speed_download}) {
    $res->body( $swml->swaig_response_json( { response => "Tell the user here are the test results. Download speed: $customer->{modem_speed_download}, Upload speed: $customer->{modem_speed_upload}" } ) );
    broadcast_by_agent_id( $agent, "valid, please continue" );
    broadcast_by_agent_id( $agent, $swml->swaig_response_json( { response => "Tell the user here are the test results. Download speed: $customer->{modem_speed_download} megabits, Upload speed: $customer->{modem_speed_upload} megabits" } ) );
} else {
    $res->body( $swml->swaig_response_json( { response => "Invalid try again speed_test" } ) );
    broadcast_by_agent_id( $agent, "invalid, use speed_test function" );
    broadcast_by_agent_id( $agent, $swml->swaig_response_json( { response => "Invalid try again speed_test" } ) );
}



return $res->finalize;
```

modem_diagnostics
-----------------------

* Name:modem_diagnostics
* Purpose:customer modem upstream downstream and snr levels
* Argument:account_number|7 digit number,cpni|4 digit number

```bash

my $env       = shift;
my $req       = Plack::Request->new( $env );
my $swml      = SignalWire::ML->new;
my $post_data = decode_json( $req->raw_body );
my $data      = $post_data->{argument}->{parsed}->[0];
my $res       = Plack::Response->new( 200 );
my $agent     = $req->param( 'agent_id' );
#Use this as a seconday function once primary function is used from the database
my $customer  = $post_data->{meta_data}->{customer};

print STDERR Dumper $data;
print STDERR $agent;

broadcast_by_agent_id( $agent, $customer );

if ($customer) {
    $res->body( $swml->swaig_response_json( { response => "Tell the user here are the test results. Downstream level: $customer->{modem_downstream_level}, Upstream level: $customer->{modem_upstream_level}, Modem SNR: $customer->{modem_snr}" } ) );
    broadcast_by_agent_id( $agent, $swml->swaig_response_json( { response => "Tell the user here are the test results. Downstream level: $customer->{modem_downstream_level}, Upstream level: $customer->{modem_upstream_level}, Modem SNR: $customer->{modem_snr}" } ) );
} else {
    $res->body( $swml->swaig_response_json( { response => "Invalid try again. Use modem-diagnostics-function" } ) );
    broadcast_by_agent_id( $agent, $swml->swaig_response_json( { response => "Invalid try again. modem-diagnostics-function" } ) );
}

return $res->finalize;
```

