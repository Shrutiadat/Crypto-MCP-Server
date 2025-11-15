# Crypto-MCP-Server
Python MCP server for real-time cryptocurrency market data
# 🚀 Cryptocurrency MCP Server

A Python-based Model Context Protocol (MCP) server that provides real-time and historical cryptocurrency market data from major exchanges. Built for AI assistants to access live crypto prices and market information.

[![Python](https://img.shields.io/badge/Python-3.8%2B-blue)](https://www.python.org/)
[![CCXT](https://img.shields.io/badge/CCXT-4.0%2B-green)](https://github.com/ccxt/ccxt)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Setup & Installation](#setup--installation)
- [Usage](#usage)
- [MCP Tools](#mcp-tools)
- [Testing](#testing)
- [Approach & Design Decisions](#approach--design-decisions)
- [Assumptions](#assumptions)
- [Project Structure](#project-structure)
- [Demo](#demo)

## 🎯 Overview

This project implements a Model Context Protocol (MCP) server that enables AI assistants (like Claude, ChatGPT) to access real-time cryptocurrency market data. The server uses the CCXT library to connect to cryptocurrency exchanges and exposes data through standardized MCP tools.

**Built for:** Internship Application Assignment  
**Technology Stack:** Python, MCP SDK, CCXT, Kraken Exchange API  
**Status:** ✅ Fully Functional - 100% Test Pass Rate

## ✨ Features

- ✅ **Real-time Price Fetching**: Get current prices for any cryptocurrency
- ✅ **Historical Data**: Retrieve OHLCV data with configurable timeframes
- ✅ **Multi-Cryptocurrency Support**: Query multiple coins simultaneously
- ✅ **Smart Caching**: 60-second cache to reduce API calls and improve performance
- ✅ **Robust Error Handling**: Graceful handling of network issues and invalid symbols
- ✅ **Market Summaries**: Aggregate data across multiple cryptocurrencies
- ✅ **Global Compatibility**: Works worldwide including restricted regions (India)
- ✅ **Comprehensive Testing**: Full test suite with 100% pass rate

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    User / AI Assistant                   │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│              MCP Server (crypto_mcp_server.py)          │
│  ┌──────────────────────────────────────────────────┐   │
│  │  MCP Tools:                                      │   │
│  │  • get_crypto_price                              │   │
│  │  • get_multiple_prices                           │   │
│  │  • get_historical_data                           │   │
│  │  • get_market_summary                            │   │
│  └──────────────────────────────────────────────────┘   │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│          Core Data Fetcher (crypto_fetcher.py)          │
│  ┌──────────────────────────────────────────────────┐   │
│  │  • Exchange Connection Management                │   │
│  │  • Symbol Normalization (BTC → XBT for Kraken)  │   │
│  │  • Smart Caching Layer                           │   │
│  │  • Error Handling & Retry Logic                  │   │
│  └──────────────────────────────────────────────────┘   │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│                CCXT Library (v4.0+)                     │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│          Kraken Exchange API (Primary)                  │
│          + 100+ Other Exchanges (Supported)             │
└─────────────────────────────────────────────────────────┘
```

## 🛠️ Setup & Installation

### Prerequisites

- Python 3.8 or higher
- pip (Python package manager)
- Internet connection

### Installation Steps

1. **Clone the repository**
```bash
git clone https://github.com/YOUR_USERNAME/crypto-mcp-server.git
cd crypto-mcp-server
```

2. **Install dependencies**
```bash
pip install -r requirements.txt
```

3. **Verify installation**
```bash
python -c "import ccxt; print('✅ CCXT installed')"
python -c "import mcp; print('✅ MCP installed')"
```

### Quick Test

```bash
# Run the test suite
python test_crypto_mcp.py
```

Expected output:
```
📊 TEST SUMMARY
✅ Passed: 10
❌ Failed: 0
📈 Success Rate: 100.0%
```

## 🎮 Usage

### As Python Library

```python
from crypto_fetcher import CryptoDataFetcher

# Initialize with Kraken exchange
fetcher = CryptoDataFetcher('kraken')

# Get Bitcoin price
btc = fetcher.get_current_price('BTC/EUR')
print(f"Bitcoin: €{btc['price']:,.2f}")

# Get multiple prices
prices = fetcher.get_multiple_prices(['BTC/EUR', 'ETH/EUR', 'LTC/EUR'])
for p in prices:
    print(f"{p['symbol']}: €{p['price']:,.2f}")

# Get historical data (last 7 days)
history = fetcher.get_historical_data('BTC/EUR', '1d', 7)
print(f"Historical data points: {len(history)}")
```

### As MCP Server

```bash
# Start the MCP server
python crypto_mcp_server.py
```

The server will listen on stdio and respond to MCP protocol requests.

## 🔧 MCP Tools

### 1. get_crypto_price

Get current price for a single cryptocurrency.

**Input:**
```json
{
  "symbol": "BTC/EUR"
}
```

**Output:**
```json
{
  "symbol": "BTC/EUR",
  "price": 82650.0,
  "high_24h": 83228.6,
  "low_24h": 80790.1,
  "volume_24h": 515.29,
  "timestamp": "2025-11-15T17:40:37.959319",
  "exchange": "kraken"
}
```

### 2. get_multiple_prices

Get prices for multiple cryptocurrencies at once.

**Input:**
```json
{
  "symbols": ["BTC/EUR", "ETH/EUR", "LTC/EUR"]
}
```

**Output:**
```json
[
  {"symbol": "BTC/EUR", "price": 82650.0, ...},
  {"symbol": "ETH/EUR", "price": 2751.44, ...},
  {"symbol": "LTC/EUR", "price": 88.50, ...}
]
```

### 3. get_historical_data

Get historical OHLCV (Open, High, Low, Close, Volume) data.

**Input:**
```json
{
  "symbol": "BTC/EUR",
  "timeframe": "1d",
  "limit": 7
}
```

**Supported timeframes:** `1m`, `5m`, `15m`, `1h`, `4h`, `1d`, `1w`

**Output:**
```json
[
  {
    "timestamp": "2025-11-11T00:00:00",
    "open": 91690.5,
    "high": 92934.0,
    "low": 88448.4,
    "close": 88941.2,
    "volume": 400.15
  },
  ...
]
```

### 4. get_market_summary

Get aggregated market data for multiple cryptocurrencies.

**Input:**
```json
{
  "symbols": ["BTC/EUR", "ETH/EUR"]
}
```

**Output:**
```json
{
  "total_queried": 2,
  "successful": 2,
  "failed": 0,
  "prices": [...],
  "errors": [],
  "timestamp": "2025-11-15T17:40:40.242449"
}
```

## 🧪 Testing

The project includes a comprehensive test suite covering all functionality.

### Run All Tests

```bash
python test_crypto_mcp.py
```

### Test Coverage

- ✅ Exchange initialization and connection
- ✅ Single cryptocurrency price fetching
- ✅ Multiple cryptocurrency queries
- ✅ Historical data retrieval
- ✅ Cache functionality and expiration
- ✅ Error handling for invalid symbols
- ✅ Market summary generation
- ✅ All MCP tool integrations

### Test Results

```
✅ Passed: 10/10
📈 Success Rate: 100.0%
```

## 💡 Approach & Design Decisions

### 1. Exchange Selection: Kraken

**Why Kraken?**
- ✅ Global availability (works in India and restricted regions)
- ✅ No API keys required for public data
- ✅ High uptime (99.9%)
- ✅ Comprehensive market coverage
- ✅ Reliable rate limiting

**Alternative:** Binance was initially considered but is restricted in India (HTTP 451 error).

### 2. Caching Strategy

**Implementation:** 60-second in-memory cache

**Rationale:**
- Reduces API calls to exchange (respects rate limits)
- Improves response time for repeated queries
- Balances data freshness vs. performance
- Simple implementation without external dependencies

### 3. Symbol Normalization

**Challenge:** Kraken uses `XBT` instead of `BTC`

**Solution:** Automatic symbol mapping
```python
# User requests: BTC/EUR
# System internally uses: XBT/EUR (Kraken's format)
# Response shows: BTC/EUR (user's requested format)
```

### 4. Error Handling

**Multi-layer approach:**
1. **Exchange level:** Handles connection errors, rate limits
2. **Symbol level:** Validates trading pairs, tries alternatives
3. **Data level:** Handles missing fields (volume, etc.)
4. **User level:** Returns clear error messages with context

### 5. Modular Architecture

**Separation of concerns:**
- `crypto_fetcher.py`: Core data fetching logic
- `crypto_mcp_server.py`: MCP protocol implementation
- `test_crypto_mcp.py`: Comprehensive testing

**Benefits:**
- Easy to test individual components
- Simple to add new exchanges
- Clear code organization
- Reusable components

## 📝 Assumptions

### Technical Assumptions

1. **Internet Connectivity**: Stable internet connection available
2. **API Availability**: Kraken API is accessible and operational
3. **Rate Limits**: Public API limits are sufficient for use case
4. **Data Freshness**: 60-second cache is acceptable for most queries
5. **Currency Pairs**: EUR pairs are used as primary (globally available)

### Functional Assumptions

1. **Use Case**: Primarily for informational queries, not high-frequency trading
2. **User Base**: AI assistants and developers needing crypto data
3. **Volume**: Moderate query volume (within public API limits)
4. **Authentication**: No user authentication required (public data only)
5. **Persistence**: No long-term data storage needed

### Deployment Assumptions

1. **Environment**: Can run on any system with Python 3.8+
2. **Dependencies**: All required packages available via pip
3. **Configuration**: Default settings work for most use cases
4. **Scaling**: Vertical scaling sufficient (no distributed architecture needed)

## 📁 Project Structure

```
crypto-mcp-server/
│
├── crypto_fetcher.py          # Core data fetching module
│   ├── CryptoDataFetcher      # Main class
│   ├── Exchange initialization
│   ├── Caching logic
│   └── Data retrieval methods
│
├── crypto_mcp_server.py       # MCP server implementation
│   ├── CryptoMCPServer        # Server class
│   ├── Tool registration
│   ├── Request handling
│   └── Response formatting
│
├── test_crypto_mcp.py         # Test suite
│   ├── TestCryptoDataFetcher  # Unit tests
│   ├── TestMCPIntegration     # Integration tests
│   └── Test runner
│
├── requirements.txt           # Python dependencies
├── README.md                  # This file
└── .gitignore                # Git ignore rules
```

## 🎬 Demo

### Live Test Results

```bash
$ python test_crypto_mcp.py

🚀 CRYPTO MCP SERVER - COMPREHENSIVE TEST SUITE

TEST 1: Fetching Bitcoin Price
✅ BTC/EUR: €82,650.00
   24h High: €83,228.60
   24h Low: €80,790.10

TEST 2: Fetching Multiple Prices
✅ BTC/EUR: €82,650.00
✅ ETH/EUR: €2,752.56
✅ LTC/EUR: €88.50

TEST 3: Fetching Historical Data
✅ Fetched 7 days of data
   Latest close: €82,650.00
   Oldest close: €90,657.20

📊 TEST SUMMARY
✅ Passed: 10
❌ Failed: 0
📈 Success Rate: 100.0%

🎉 ALL TESTS PASSED!
```

## 🔍 Key Technical Highlights

1. **Smart Symbol Resolution**: Automatically handles exchange-specific naming
2. **Intelligent Caching**: Reduces API load while maintaining data freshness
3. **Graceful Degradation**: Continues working even with partial failures
4. **Production-Ready**: Comprehensive error handling and logging
5. **Well-Tested**: 100% test pass rate with automated test suite

## 🚀 Future Enhancements

- [ ] Support for multiple exchanges simultaneously
- [ ] WebSocket support for real-time streaming
- [ ] Advanced caching with Redis
- [ ] Rate limiting per user
- [ ] Historical data export (CSV/JSON)
- [ ] Price alerts and notifications
- [ ] Technical indicators (RSI, MACD, etc.)

## 📞 Support & Contact

For questions, issues, or suggestions:

- **Issues**: Open an issue on GitHub
- **Documentation**: See inline code comments
- **CCXT Docs**: https://docs.ccxt.com
- **MCP Docs**: https://modelcontextprotocol.io

## 📄 License

MIT License - Feel free to use this project for learning and development.

## 🙏 Acknowledgments

- **CCXT Team**: For the excellent cryptocurrency exchange library
- **Anthropic**: For the MCP protocol and SDK
- **Kraken**: For reliable API access
- **Community**: For feedback and testing

---

**Built with ❤️ for the Internship Application**

*Last Updated: November 15, 2025*
