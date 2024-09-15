# **Unified Backtest Engine**
Backtest engine that simulates passive &amp; aggressive orders. 

### **Markets Supported**
---------------------
**HKEX**
Data feed is dessiminated by **Orion Market Data - Cash (OMD-C)** system. This feed has the following characteristics:
1) **MBO** - granular data on individual orders (price, quantity & side)
2) **Message Types** - the order types are ADD | MODIFY | CANCEL | TRADE | SNAPSHOT | MARKET-STATUS
3) The data protocol is *binary* and includes **sequence numbering**
