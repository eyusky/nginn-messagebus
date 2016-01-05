# Introduction #

NGinn.MessageBus keeps all of its data in the database. Here is a description of NMB's tables.

# Details #

## Message queue tables ##

In NMB each queue is stored in its own database table named the same as the queue. For example, sql://db/MyQueue will be stored in MyQueue table.
When starting NGinn.MessageBus checks if queue tables are present in the database and if not, creates them.

The structure of the queue table and the table creation script looks like this:
```
CREATE TABLE [dbo].[MyQueue](
	[id] [bigint] IDENTITY(1,1) NOT NULL,
	[from_endpoint] [varchar](50) NOT NULL,
	[to_endpoint] [varchar](50) NOT NULL,
	[subqueue] [char](1) NOT NULL,
	[insert_time] [datetime] NOT NULL,
	[last_processed] [datetime] NULL,
	[retry_count] [int] NOT NULL,
	[retry_time] [datetime] NOT NULL,
	[error_info] [text] NULL,
	[correlation_id] [varchar](100) NULL,
	[label] [varchar](100) NULL,
	[msg_text] varchar(max) NULL,
	[msg_headers] varchar(max) null,
	[unique_id] varchar(40) null
CONSTRAINT [PK_MyQueue] PRIMARY KEY CLUSTERED 
(
	[id] ASC
)WITH (PAD_INDEX  = OFF, STATISTICS_NORECOMPUTE  = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS  = ON, ALLOW_PAGE_LOCKS  = ON, FILLFACTOR=35) 
) 

CREATE NONCLUSTERED INDEX [IDX_MyQueue_subqueue_retry_time] ON [dbo].[MyQueue] 
(
	[subqueue] ASC,
	[retry_time] ASC
)
INCLUDE(id)
```

And the columns:
  1. **id** - is the message id (key)
  1. **from\_endpoint** - message sender enpoint
  1. **to\_endpoint** - message destination enpoint
  1. **subqueue** - subqueue symbol: I - means message ready for processing, R - message scheduled for processing in future time, X - message processed and F - message failed all processing attempts
  1. **insert\_time** - time when message was inserted into the queue
  1. **last\_processed** - last time the message was processed
  1. **retry\_count** - number of retries made so far
  1. **retry\_time** - scheduled processing/retry time
  1. **error\_info** - error information (in case of processing failure)
  1. **correlation\_id** - message correlation id assigned by application
  1. **label** - message label (as returned by ToString())
  1. **msg\_text** - serialized message body
  1. **msg\_headers** - message headers
  1. **unique\_di** - globally unique message id

Examples of useful SQL operations you might want to perform on a queue:
```
--check how many messages are waiting in the input queue
select COUNT(*) from MQueue1 with(nolock) where subqueue='I'

--check the failed queue
select * from MQueue1 with(nolock) where subqueue='F' order by id

--move a message from failed to input queue
update MQueue1 set subqueue='I' where id=1332

--check how many messages were processed in a day
select COUNT(*) from MQueue1 with(nolock) where subqueue='X'
and retry_time between '2011-09-10 00:00:00' and '2011-09-10 23:59:59'

-- calculate average latency for last 10 minutes - average time between message 
-- being ready for processing and the time it was actually processed 
select avg(DATEDIFF(millisecond, retry_time, last_processed)) from MQueue1 with(nolock)
where DATEDIFF(minute, retry_time, getdate()) < 10 and subqueue='X'
```
As you can see, the message queue is just a normal SQL table and all operations on messages can be done in SQL. You may note the use of (nolock) hint - this is to avoid locking that could interfere with NGinn.MessageBus processing the messages.

The latency statistic is quite useful when monitoring performance - it is very easy to achieve with SQL and almost impossible with other message queuing mechanisms.

## Subscriptions table ##
If NGinn.MessageBus is configured to use SQL subscription persistence it will create a table for storing subscriptions. This table can be shared by all applications using the database. By default the table is named **NGinnMessageBus\_Subscriptions** but the name can be changed in configuration.

```
create table dbo.[NGinnMessageBus_Subscriptions] (
	publisher_endpoint varchar(200) not null,
	subscriber_endpoint varchar(200) not null,
	message_type varchar(300) not null,
	created_date datetime not null,
	expiration_date datetime null,
	CONSTRAINT [PK_NGinnMessageBus_Subscriptions] PRIMARY KEY CLUSTERED 
	(
		publisher_endpoint,
		message_type,
		subscriber_endpoint
	) 
)
```

## And that's it ##
No other database object are created by NGinn.MessageBus