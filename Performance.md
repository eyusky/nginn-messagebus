TODO complete

## Sending messages ##




### Test case description ###


Testing code:
```
public static void SendOperation1(IMessageBus bus, string connStr, int i)
{
    using (IDbConnection con = new SqlConnection(connStr))
    {
        con.Open();
        using (IDbCommand cmd = con.CreateCommand())
        {
            cmd.CommandText = string.Format("select id, name from test where id={0}", i);
            string name;
            using (IDataReader dr = cmd.ExecuteReader())
            {
                dr.Read();
                name = dr.GetString(1);
            }
            cmd.CommandText = string.Format("update Test set name='{1}' where id={0}", i, DateTime.Now.ToString());
            cmd.ExecuteNonQuery();

            bus.Send("sql://testdb1/MQueue2", new TestMessage1 { Id = i });
                
            cmd.CommandText = string.Format("insert into TestLog (tstamp, user_id, summary) values(getdate(), 'I', 'send operation {0}')", i);
            cmd.ExecuteNonQuery();

            bus.Send("sql://testdb1/MQueue2", new TestMessage2 { Text = "first " + i });
            bus.Send("sql://testdb1/MQueue2", new TestMessage2 { Text = "second " + i });
        }
    }
}
```

This code executes 3 operations on application database (select, insert and update) and sends 3 messages to nginn-messagebus queue.
