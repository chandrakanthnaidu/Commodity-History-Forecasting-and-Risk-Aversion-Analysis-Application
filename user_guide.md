# User Guide: Commodity History, Forecasting, and Risk Aversion Analysis Application

Hi! I’m Chandrakanth Naidu Gonuguntla, the developer behind the **Commodity History, Forecasting, and Risk Aversion Analysis Application**, built as a graduate research assistant under Prof. Santosh Mahapatra. This guide walks you through using the application, which I created to help you analyze commodity prices, forecast trends, and optimize contract allocations using the **FRED API**. I’ve designed it to be intuitive, and this guide mirrors the demo video ([Insert YouTube Link]) to show you exactly how to navigate the app. Let’s dive in!

## What Is This Application?

This web app lets you:
- **Analyze Historical Data**: Visualize commodity price trends with interactive Plotly graphs.
- **Forecast Prices**: Use **Exponential Smoothing** and **Prophet** to predict future prices, with metrics like MAE and RMSE.
- **Manage Risks**: Calculate optimal contract allocations for two commodities based on risk preferences.

I built the backend with **Flask** and **Python**, using libraries like `statsmodels`, `prophet`, and `pandas`. The frontend uses **HTML**, **CSS**, **JavaScript**, and **Plotly** for a seamless experience. Below, I’ll guide you through the user flow, just like in the demo.

## Getting Started

1. **Access the App**: Run the app locally (`http://localhost:5000`) after following the setup instructions in the [GitHub README]([Insert GitHub Link]).
2. **Prerequisites**: You’ll need a FRED API key (free from [fred.stlouisfed.org](https://fred.stlouisfed.org/)) and Python 3.8+.

## User Flow: Step-by-Step Guide

### 1. Home Page
When you launch the app, you’ll land on `home.html`. This is the hub for all features:
- **Options**: Click links to:
  - **Commodity History**: View past price trends.
  - **Exponential Smoothing**: Forecast prices with customizable parameters.
  - **Prophet Forecasting**: Forecast with advanced trend and seasonality models.
- **Screenshot**:  
  ![Home Page](images/home.png)  
  *Choose your analysis type.*

### 2. Commodity History
Let’s explore historical data first, as shown in the demo:
- **Select Commodity**: Choose a commodity (e.g., “X”) from the dropdown.
- **Choose Titles**: Check up to three titles (e.g., regions for “X”). I selected three to show variety.
- **View Graph**: Click to display a Plotly graph plotting all three titles’ historical prices.  
  ![Historical Data](images/historical_data.png)  
  *Historical prices for three titles.*
- **Filter Dates**: Select start and end dates to zoom in on specific periods.
- **Download**:
  - **Excel**: Export historical data as an `.xlsx` file.
  - **Image**: Save the graph as a PNG.
- **Why It’s Useful**: This helps you understand past trends before forecasting.

### 3. Exponential Smoothing Forecasting
Next, let’s forecast prices using Exponential Smoothing, a model I implemented for its flexibility:
- **Select Commodity and Titles**: Choose “X” and the same three titles for consistency.
- **Set Dates**:
  - **Training Start/End Dates**: Define the historical data to train the model.
  - **Forecast Period**: Specify how far to predict (e.g., 6 months).
- **Configure Parameters**:
  - **Trend**: None, additive, or multiplicative.
  - **Seasonality**: None, additive, or multiplicative.
  - **Smoothing Levels**: Adjust weights for recent data (0 to 1).
- **Run Forecast**: Click to generate results. You’ll see:
  - **Graph**:  
    ![Exponential Smoothing Forecast](images/exponential_forecast.png)  
    *Training (left), test (right), and forecast (dotted lines).*
    - **Left of Dotted Line**: Training data used to build the model.
    - **Right of Dotted Line**: Test data (to evaluate accuracy) and forecast data (dotted lines after a gap).
  - **Metrics**: MAE, RMSE, MAPE, etc., to assess forecast quality.
- **Download**:
  - **PDF**: Parameters, metrics, and graph.
  - **Excel**: Forecast values and metrics.
- **Risk Aversion Prep**:
  - Select two titles as “Source 1” and “Source 2” (e.g., two regions of “X”).
  - Click “Go to Risk Aversion” to auto-populate parameters (P1T, P2T, P10, P20, P0) for the next step.

### 4. Risk Aversion Analysis
Now, let’s optimize contract allocations, a feature I designed for risk-conscious users:
- **Pre-Filled Parameters**: If coming from Exponential Smoothing, parameters like forecasted prices (P1T, P2T) and historical prices (P10, P20) are auto-filled.
- **Input Additional Parameters**:
  - **Variance**: Price volatility for one commodity.
  - **Contract Duration**: Length of the contract (e.g., 12 months).
- **Calculate**: Click to compute:
  - **Moderate Risk Aversion (MRA)**: Allocation for moderate risk tolerance.
  - **High Risk Aversion (HRA)**: Allocation for high risk aversion.
  - **Formulas**: Detailed calculations (e.g., growth rates, volatility) are displayed.
  - **Screenshot**:  
    ![Risk Aversion](images/risk_aversion.png)  
    *Allocation proportions and formulas.*
- **Download**: Save results as a PDF.
- **Why It’s Useful**: This helps you balance cost and risk when choosing suppliers.

### 5. Prophet Forecasting
Prophet forecasting follows a similar flow to Exponential Smoothing, with advanced features:
- **Select Commodity and Titles**: Choose “X” and three titles.
- **Set Dates**: Training start/end dates and forecast period.
- **Configure Parameters**:
  - **Growth**: Linear or logistic.
  - **Changepoint Prior Scale**: Flexibility for trend changes.
  - **Seasonality**: Yearly, weekly, or daily; additive or multiplicative.
- **Run Forecast**: Results include:
  - **Graph**: Training, test, and forecast data, with confidence intervals (shaded areas).  
    ![Prophet Forecast](images/prophet_forecast.png)  
    *Forecast with confidence intervals.*
  - **Metrics**: MAE, RMSE, MAPE, etc.
- **Download**: PDF and Excel, as before.
- **Risk Aversion**: Select two titles and click “Go to Risk Aversion” to repeat the risk analysis.

### 6. Risk Aversion (Again)
Repeat the risk aversion steps with Prophet’s forecasted prices:
- Input variance and contract duration.
- Calculate MRA and HRA.
- Download the PDF.

## Tips for Success
- **Check Data Availability**: Ensure your commodity has sufficient data in FRED. I’ve built error messages to alert you if data is missing.
- **Validate Inputs**: Use positive numbers for parameters like variance to avoid errors.
- **Explore Parameters**: Experiment with Exponential Smoothing’s smoothing levels or Prophet’s changepoint scale to improve accuracy.
- **Use the Demo**: Watch the YouTube video ([Insert YouTube Link]) to see me walk through each step.

## Behind the Scenes
As the developer, I built every component:
- **Backend**: I wrote Flask routes (e.g., `/run_exponential_forecast`) to fetch FRED data, run models, and generate JSON for graphs.  
  ![Code Snippet](images/code_snippet.png)  
  *Sample Flask route.*
- **Frontend**: I designed the UI to be responsive, using JavaScript to handle dynamic updates (e.g., fetching titles) and Plotly for graphs.
- **Challenges**: I debugged issues like date formatting, Excel download errors, and risk aversion edge cases, ensuring a robust experience.

## Get Involved
- **Try It**: Clone the repo ([Insert GitHub Link]), follow the README, and run the app.
- **Feedback**: Share ideas for features (e.g., new models) via GitHub issues or LinkedIn.
- **Contribute**: Submit pull requests to enhance the code.

## Acknowledgments
This project wouldn’t exist without Prof. Santosh Mahapatra’s guidance and the FRED API’s reliable data. Thanks for using my application—I hope it empowers your commodity analysis!

[Your Name] | [Your Email] | [Your LinkedIn Profile]