How the Model Was Built:
I am a first-year electrical engineering student interested in power market analysis and energy trading. This is my second forecasting project—my first attempt didn't hit the accuracy I wanted, so I built this version to bridge the gap between power system theory and actual Indian Energy Exchange (IEX) market data.

The One-Day Baseline (Why a small dataset flatlines)
The project started with a single day of 15-minute DAM market data from June 2026. After basic preprocessing, exploratory data analysis, and training an XGBoost regressor, the initial results were way off. The model completely missed the real price spikes, resulting in a flat line on the evaluation graph and a high RMSE of 4,092.39 Rs/MWh.

The problem comes down to basic power grid context. A single day of data provides only 96 time blocks, which isn't enough for a machine learning model to learn from. It can't distinguish between a weekday morning peak load ramp, a weekend demand drop, or baseline generation availability. Because it lacked historical context, XGBoost simply predicted the mathematical average of the day's prices to minimize its error.

Phase 2: Expanding to a 30-Day Timeline
To give the model enough history to learn patterns, I pulled a continuous 30-day dataset from the IEX portal spanning late May to June 2026, giving the script enough operational rows to train properly.

With a month of data, the model's tracking improved immediately and the RMSE dropped significantly. By looking at a full multi-week timeline, the model began recognizing daily consumer habits—specifically the classic "double-hump" pricing curve that happens in India as demand peaks during morning routines and late evening lighting hours.

Phase 3: Transitioning from 1-Hour to 3-Hour Rolling Windows
Even with a month of data, the model was still lagging behind sudden price movements because it lacked real-time situational awareness. To fix this, I added a 1-hour rolling average to the demand-supply gap. This dropped the RMSE further because it gave the model a short-term memory of market momentum (since the previous hour's bidding heavily influences the current block).

However, a 1-hour window proved too erratic. Physical power grids don't shift in isolated 60-minute blocks. Large thermal power plants require sustained periods to ramp their generation up or down, and major regional load shifts (like afternoon cooling loads) usually span across several hours.

By expanding the feature to a 3-Hour Rolling Window (12 time blocks of 15 minutes), the timeline finally aligned with the actual physical ramping constraints of the grid. This smoothed out the high-frequency noise and brought the final optimized RMSE down to 796.44 Rs/MWh—an overall error reduction of 80.5% compared to the baseline.

Future Scope for Trading Precision
While an error margin of 796.44 Rs/MWh is a solid milestone for a standalone model, a commercial energy trading desk requires deeper fundamental inputs. To take this project further, three key variables should be integrated:

Regional Weather Data: Power demand in India is heavily temperature-driven. Adding ambient temperature and humidity data across major industrial load hubs would allow the model to anticipate air-conditioning spikes before they hit the bidding sheets.

Renewable Generation Profiles: High solar and wind injections drastically alter net load. Massive solar generation frequently pushes market clearing prices down during the middle of the day. Adding real-time solar and wind generation forecasts would prevent the model from overestimating afternoon prices.

Transmission Congestion Constraints: Electricity can only flow if the physical lines have available capacity. Factoring in Inter-Regional Available Transfer Capability (ATC) margins would help the model predict localized price decoupling across separate bidding zones when transmission lines get congested.**
