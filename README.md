# **Unified Backtest Engine**
Backtest engine that simulates passive &amp; aggressive orders. The general design is as such:

#### **a) Market Data Reader (MDr)**
    - Input: *market type*
    - Function: converts raw market data file(s) into a *unified format* of just ADD | MODIFY | CANCEL | TRADES.
                It also attaches *attributes* such as counterpartyId, timePlaced, timeSinceTick etc onto the order.  

#### **b) Internal Order Book (iOB)**
    - Inputs (we specific 3 main things)
          - eventType                 ... PLACE | CANCEL | FILL
          - counterpartyId to record  ... Optiver | Jane | Citadel etc.
          - attributes to record      ... volume | price | creditBps | adjustment | priority ..
    - Function:
          - This receives the *unified* order book events from the **MDr** as well as *order requests* from our **OEE**
          - It matches the orders based on exchange priority/matching rules
          - After matching, it *dispatches* the events to the **VL** layer | **PnL tracker** (own trades as well as updated traded price)
    - LOGGER:
          - This outputs a log.txt file which can be easily converted to parquet files
          
#### **c) Valuation layer (VL)**
    - Receives MBO feed from the **iOB** and calculates features/alphas for valuation
    - Calculates the valuation (using XGB or whatever model) and sends this to the **OEE** as a raw-valuation.

#### **d) Order Execution Engine (OEE)**
    - this is customizable by the trader and deals w/ the SIZING based on the valuation. The main logic it deals with is:
        1) internal credit-volume logic 
        2) *placement* logic
        3) *pulling* logic
        4) *adjustment* logic 
        5) *risk-limit* logic 
    - It sends the order request to the **iOB** for matching/execution

#### **e) PnL Tracker**
    - This keeps a track of our *PnL*, *Position* as well as various *Risk* metrics.
    - It receives our order fill history from **iOB** to calculate all of the above. 
    - Finally the *Pnl Tracker* dispatches the events to the **iOB** for *logging* as well as to the **OEE**

---------------------
### **Markets Supported**

**HKEX**
Data feed is dessiminated by **Orion Market Data - Cash (OMD-C)** system. This feed has the following characteristics:
1) **MBO** - granular data on individual orders (price, quantity & side)
2) **Message Types** - the order types are ADD | MODIFY | CANCEL | TRADE | SNAPSHOT | MARKET-STATUS
3) The data protocol is *binary* and includes **sequence numbering**
