# Detailed Commodity Forecasting Project Report

## Overview
This project is a web-based application designed to analyze historical commodity price data, generate forecasts using Exponential Smoothing and Prophet models, and perform risk aversion analysis for contract allocation. It integrates data from the Federal Reserve Economic Data (FRED) API, processes it using Python-based forecasting models, and presents results through an interactive frontend. The application supports visualizing historical data, running forecasts, downloading results in PDF/Excel formats, and calculating optimal contract allocations based on risk preferences.

The application is built with a **Flask backend** (Python) for data processing and forecasting, and a **frontend** using HTML, CSS, JavaScript, and external libraries (Plotly, jsPDF, html2canvas, jQuery, MathJax). Below, I will detail the architecture, user flow, calculations, and setup instructions.

## Frontend and Backend Integration

### Frontend
The frontend provides an interactive user interface for selecting commodities, configuring forecasts, visualizing results, and performing risk aversion analysis. It consists of:

- **HTML Templates** (in `templates/`):
  - `home.html`: The landing page, redirecting to forecasting or historical analysis pages.
  - `fred_commodity_history.html`: Displays historical commodity price data in graphs and tables.
  - `fred_exponential_forecast.html`: Interface for running Exponential Smoothing forecasts.
  - `fred_prophet_forecast.html`: Interface for Prophet forecasting, including parameter configuration, result visualization, and risk aversion analysis.
  - `riskaversion.html`: Calculates contract allocation proportions based on forecasted prices and risk aversion levels.

- **CSS** (in `static/`):
  - `fred_expo_styling.css`: Defines consistent styling for layout, forms, tables, buttons, and responsive design. Key styles include:
    - `.home-button`: A fixed-position button for navigation.
    - `.form-group`, `.form-row`: Structured form layouts.
    - `.metrics-table-container`: Styled tables for forecast metrics.
    - `.loading-spinner`: Animated loading indicator during backend requests.

- **JavaScript** (embedded in HTML templates):
  - **Functionality**:
    - Dynamically updates dropdowns (e.g., commodity titles, dates) based on user selections.
    - Sends HTTP requests to backend endpoints using the `fetch` API.
    - Renders Plotly graphs for historical and forecasted data.
    - Generates PDF reports using jsPDF and html2canvas (captures graphs).
    - Downloads Excel files for forecast data.
    - Handles risk aversion calculations and UI updates.
  - **Key Functions** (in `fred_prophet_forecast.html` and `home.html`):
    - `fetchTitles()`: Fetches commodity titles from `/get_commodity_titles`.
    - `fetchCommodityDates()`: Retrieves available dates for selected titles and frequency.
    - `runForecast()`: Sends forecasting parameters to `/run_prophet_forecast` and renders results.
    - `renderResults()`: Updates the UI with metrics tables and Plotly graphs.
    - `downloadParametersPDF()`: Generates a PDF with parameters, metrics, and graphs.
    - `downloadForecastExcel()`: Requests an Excel file from `/download_prophet_forecast_data`.
    - `goToRiskAversion()`: Navigates to the risk aversion page with pre-filled parameters.
  - **External Libraries**:
    - **Plotly** (`plotly-2.32.0.min.js`): Renders interactive graphs.
    - **jsPDF** (`jspdf.umd.min.js`): Generates PDF downloads.
    - **html2canvas** (`html2canvas.min.js`): Captures graph images for PDFs.
    - **jQuery** (`jquery-3.6.0.min.js`): Simplifies DOM manipulation and AJAX in `riskaversion.html`.
    - **MathJax** (`tex-mml-chtml.js`): Renders mathematical equations in `riskaversion.html`.

- **Error Handling**:
  - Displays errors in a styled `#error-msg` div (e.g., "No data available", "Forecast failed").
  - Validates user inputs (e.g., ensures at least one title is selected, positive numbers for risk aversion).
  - Shows loading spinners (`#loading`) during backend requests.

### Backend
The backend is a Flask application (`app.py`) that handles data retrieval, forecasting, and result generation. It uses Python libraries for data processing and forecasting.

- **Dependencies**:
  - `flask`: Web framework for serving pages and handling API requests.
  - `fredapi`: Fetches historical commodity price data from FRED.
  - `pandas`, `numpy`: Data manipulation and numerical computations.
  - `statsmodels`: Implements Exponential Smoothing (`HoltWinters`).
  - `prophet`: Implements Prophet forecasting.
  - `plotly`: Generates JSON for interactive plots.
  - `openpyxl`: Creates Excel files for forecast downloads.
  - `python-dotenv`: Loads environment variables (e.g., FRED API key).

- **Key Endpoints**:
  - **`/get_commodities`**: Returns a list of available commodities.
  - **`/get_commodity_titles`**: Fetches titles for a selected commodity.
  - **`/get_title_data`**: Retrieves historical price data for a title and frequency.
  - **`/get_exponential_commodity_dates`**: Provides valid dates for Exponential Smoothing.
  - **`/run_exponential_forecast`**: Runs Exponential Smoothing forecasts and returns results (metrics, plot JSON).
  - **`/get_prophet_commodity_dates`**: Provides valid dates for Prophet forecasting.
  - **`/run_prophet_forecast`**: Runs Prophet forecasts, checks data continuity, and returns results (metrics, plot JSON).
  - **`/download_prophet_forecast_data`**: Generates an Excel file with forecast data.
  - **`/risk_aversion`**: Calculates risk aversion metrics and contract allocation proportions.
  - **`/download_RAC_pdf`**: Generates a PDF with risk aversion results.

- **Data Processing**:
  - Fetches historical data from FRED using `fredapi`.
  - Cleans and aggregates data based on user-selected frequency (e.g., Monthly, Daily).
  - Performs forecasting using `statsmodels` (Exponential Smoothing) or `prophet`.
  - Computes metrics like MAE, RMSE, MAPE, historical variance, and growth rates.
  - Generates Plotly JSON for graphs, including historical values, forecasts, and confidence intervals.

- **Error Handling**:
  - Returns JSON responses with `success: false` and `error` messages for invalid inputs or data issues.
  - Validates inputs (e.g., positive numbers, sufficient data points).
  - Handles missing or discontinuous data by returning warnings or errors.

### Integration
The frontend and backend integrate via HTTP requests (primarily POST) using JSON:

1. **User Input**:
   - Users interact with forms (e.g., select commodities, set date ranges, configure parameters).
   - JavaScript validates inputs and sends them to backend endpoints using `fetch`.

2. **Backend Processing**:
   - Flask routes process requests, fetching data from FRED or running forecasts.
   - Results (metrics, plot JSON, errors) are returned as JSON responses.

3. **Frontend Rendering**:
   - JavaScript parses responses, updates the UI with tables and metrics, and renders Plotly graphs.
   - Download buttons trigger additional requests for PDF/Excel files, served as downloadable blobs.

4. **Example Flow** (Prophet Forecast):
   - User selects a commodity, titles, frequency, and parameters in `fred_prophet_forecast.html`.
   - JavaScript sends a POST request to `/run_prophet_forecast` with parameters (e.g., `titles`, `train_start_date`, `changepoint_prior_scale`).
   - Backend fetches historical data, runs Prophet, computes metrics (MAE, RMSE), and generates a Plotly JSON plot.
   - Response includes `success`, `results` (metrics, forecast values), and `plot` (JSON).
   - Frontend renders metrics in tables, plots the graph, and enables download buttons.

This integration ensures a robust, interactive experience, with clear feedback for users and efficient data processing.

## User Flow

### Overview
The user flow describes the steps a user takes to interact with the application, from landing on the home page to generating forecasts and performing risk aversion analysis. Below, I detail the flow, highlighting frontend and backend interactions.

### Step-by-Step User Flow

1. **Access Home Page** (`home.html`):
   - **Frontend**:
     - User lands on `http://localhost:5000/`, rendered by `app.py` (`render_template('home.html')`).
     - Displays links to historical analysis (`fred_commodity_history.html`), Exponential Smoothing (`fred_exponential_forecast.html`), and Prophet forecasting (`fred_prophet_forecast.html`).
   - **Backend**:
     - Serves `home.html` with a list of commodities fetched from `/get_commodities`.

2. **Select Forecasting Type**:
   - **Frontend**:
     - User clicks a link to navigate to, e.g., `fred_prophet_forecast.html`.
     - Page loads with a form for commodity selection, title checkboxes, date ranges, and Prophet parameters.
   - **Backend**:
     - Serves the selected HTML template.

3. **Select Commodity and Titles**:
   - **Frontend**:
     - User selects a commodity from `#commodity-select`.
     - JavaScript triggers `fetchTitles()`, sending a POST request to `/get_commodity_titles` with the commodity name.
     - Response populates `#title-checkboxes` with title options (up to 5 can be selected).
     - User checks titles, and `updateFrequencyDropdown()` filters common frequencies across selected titles.
   - **Backend**:
     - `/get_commodity_titles` queries FRED for titles and metadata, returning a JSON object with titles and frequencies.
     - Example response: `{ "title1": { "metadata": {...}, "frequencies": ["Monthly", "Daily"] }, ... }`.

4. **Configure Frequency and Dates**:
   - **Frontend**:
     - User selects a frequency from `#frequency-select`.
     - `fetchCommodityDates()` sends a POST request to `/get_prophet_commodity_dates` with selected titles and frequency.
     - Response populates `#train-start-date` and `#train-end-date` dropdowns with valid dates.
     - User selects training start/end dates and test/forecast periods.
     - `checkContinuity()` sends a request to `/run_prophet_forecast` with `check_continuity: true` to validate data continuity.
   - **Backend**:
     - `/get_prophet_commodity_dates` fetches historical data for titles, computes valid date ranges, and returns `dates`, `min_date`, `max_date`.
     - `/run_prophet_forecast` (with `check_continuity`) checks for missing data points, returning warnings if gaps exist.

5. **Configure Forecasting Parameters**:
   - **Frontend**:
     - User sets Prophet parameters (e.g., `growth`, `changepoint_prior_scale`, `seasonality_mode`) in the form.
     - Parameters are validated (e.g., positive numbers, valid ranges).
   - **Backend**:
     - No interaction until the forecast is run.

6. **Run Forecast**:
   - **Frontend**:
     - User clicks `#run-forecast`, triggering `runForecast()`.
     - Sends a POST request to `/run_prophet_forecast` with all parameters (titles, dates, Prophet settings).
     - Shows a loading spinner (`#loading`).
     - On success, `renderResults()` updates the UI:
       - Displays metrics (MAE, RMSE, etc.) in `#metrics-container`.
       - Renders a Plotly graph in `#graph-container`.
       - Shows risk aversion section (`#risk-aversion-section`) if ≥2 titles are selected.
   - **Backend**:
     - `/run_prophet_forecast`:
       - Fetches historical data for each title.
       - Runs Prophet forecasting with specified parameters.
       - Splits data into training and test sets, computes forecasts.
       - Calculates metrics (e.g., MAE, RMSE, historical variance).
       - Generates Plotly JSON for historical, test, and forecast data with confidence intervals.
       - Returns JSON: `{ success: true, results: [{ title, metrics, historical_values, forecast_values, ... }], plot: { data, layout } }`.
     - Handles errors (e.g., insufficient data, invalid parameters) by returning `success: false` and `error`.

7. **View Results**:
   - **Frontend**:
     - Metrics tables show historical growth rate, variance, forecast errors (MAE, MSE, RMSE, MAPE, MAD, RSFE, Tracking Signal).
     - Plotly graph displays historical values, test period forecasts, and future forecasts with confidence intervals.
     - Risk aversion section allows selecting two commodities for comparison, updating `#risk-metrics` with metrics (P1T, P10, P0, variances).
   - **Backend**:
     - No further interaction unless downloads or risk aversion is triggered.

8. **Download Results**:
   - **Frontend**:
     - User clicks `#download-params` to trigger `downloadParametersPDF()`:
       - Uses jsPDF to create a PDF with parameters, metrics, and a graph snapshot (via html2canvas).
       - Saves as `Prophet_Forecast_Parameters.pdf`.
     - User clicks `#download-forecast` to trigger `downloadForecastExcel()`:
       - Sends a POST request to `/download_prophet_forecast_data` with forecast results.
       - Downloads an Excel file named `Prophet_Forecast_[title]_[date].xlsx`.
   - **Backend**:
     - `/download_prophet_forecast_data`:
       - Creates an Excel file using `openpyxl`.
       - Includes historical values, forecast values, upper/lower bounds, and metrics.
       - Returns a binary blob for download.

9. **Risk Aversion Analysis**:
   - **Frontend**:
     - User selects two commodities in `#first-commodity` and `#second-commodity`.
     - `updateRiskMetrics()` calculates initial price ratio (P0 = P10/P20) and displays metrics.
     - User clicks `#go-risk-aversion`, navigating to `/risk_aversion` with parameters (P1T, P2T, P10, P20, forecast time, P0).
     - On `riskaversion.html`:
       - Form is pre-populated with parameters.
       - User enters additional inputs (variance, contract duration) and clicks "Proportion of Order Allocation".
       - JavaScript sends a POST request to `/risk_aversion` via AJAX.
       - Results are displayed in `#growth-rates-container`, `#mra-container`, `#hra-container`, showing growth rates, volatility, and allocation proportions.
       - User can download a PDF via `downloadPDF()`.
   - **Backend**:
     - `/risk_aversion`:
       - Calculates growth rates, volatility, and allocation proportions for moderate and high risk aversion.
       - Returns JSON with results (mra, hra) or errors (e.g., negative discriminant).
     - `/download_RAC_pdf`:
       - Generates a PDF with risk aversion results using `reportlab`.
       - Returns a binary blob for download.

### User Flow Diagram
```
Home -> Select Forecasting Type (Prophet/Exponential/History)
  -> Select Commodity -> Fetch Titles (Backend: /get_commodity_titles)
  -> Select Titles (≤5) -> Update Frequencies
  -> Select Frequency -> Fetch Dates (Backend: /get_prophet_commodity_dates)
  -> Set Dates & Parameters -> Check Continuity (Backend: /run_prophet_forecast)
  -> Run Forecast (Backend: /run_prophet_forecast)
  -> View Results (Metrics, Graph)
  -> Download PDF/Excel (Backend: /download_prophet_forecast_data)
  -> Risk Aversion (Select 2 Commodities)
    -> Navigate to Risk Aversion Page
    -> Enter Parameters -> Calculate (Backend: /risk_aversion)
    -> View Results -> Download PDF (Backend: /download_RAC_pdf)
```

## Forecasting Parameters and Calculations

### Exponential Smoothing

#### Overview
Exponential Smoothing forecasts future values by weighting past observations, with recent data given higher weights. The `statsmodels` library implements the Holt-Winters model, supporting trends and seasonality.

#### Parameters
- **Trend**:
  - None: No trend component.
  - Additive: Linear trend added to the level.
  - Multiplicative: Trend scales with the level.
- **Seasonal**:
  - None: No seasonal component.
  - Additive: Constant seasonal variations.
  - Multiplicative: Seasonal variations scale with the level.
- **Seasonal Periods**: Number of periods in a seasonal cycle (e.g., 12 for monthly data).
- **Smoothing Level (Alpha)**: Weight for recent observations (0 to 1).
- **Smoothing Trend (Beta)**: Weight for trend component (0 to 1).
- **Smoothing Seasonal (Gamma)**: Weight for seasonal component (0 to 1).
- **Damped Trend**: Applies a damping factor to reduce trend impact over time.

#### Formulas
The Holt-Winters model has three components: level (\(L_t\)), trend (\(b_t\)), and seasonal (\(s_t\)).

- **Level**:
  \[
  L_t = \alpha (y_t - s_{t-m}) + (1 - \alpha)(L_{t-1} + b_{t-1}) \quad (\text{Additive})
  \]
  \[
  L_t = \alpha \frac{y_t}{s_{t-m}} + (1 - \alpha)(L_{t-1} \cdot b_{t-1}) \quad (\text{Multiplicative})
  \]
  - \(y_t\): Observed value at time \(t\).
  - \(m\): Seasonal periods.
  - \(\alpha\): Smoothing level.

- **Trend**:
  \[
  b_t = \beta (L_t - L_{t-1}) + (1 - \beta)b_{t-1} \quad (\text{Additive})
  \]
  \[
  b_t = \beta \frac{L_t}{L_{t-1}} + (1 - \beta)b_{t-1} \quad (\text{Multiplicative})
  \]
  - \(\beta\): Smoothing trend.
  - If damped, multiply by \(\phi\) (damping factor, 0 < \(\phi\) < 1).

- **Seasonal**:
  \[
  s_t = \gamma (y_t - L_t) + (1 - \gamma)s_{t-m} \quad (\text{Additive})
  \]
  \[
  s_t = \gamma \frac{y_t}{L_t} + (1 - \gamma)s_{t-m} \quad (\text{Multiplicative})
  \]
  - \(\gamma\): Smoothing seasonal.

- **Forecast**:
  \[
  \hat{y}_{t+h} = L_t + h b_t + s_{t+h-m} \quad (\text{Additive})
  \]
  \[
  \hat{y}_{t+h} = (L_t \cdot b_t^h) \cdot s_{t+h-m} \quad (\text{Multiplicative})
  \]
  - \(h\): Forecast horizon.
  - If damped, adjust trend: \(b_t \cdot \phi^h\).

#### Metrics
- **Mean Absolute Error (MAE)**:
  \[
  \text{MAE} = \frac{1}{n} \sum_{i=1}^n |y_i - \hat{y}_i|
  \]
- **Mean Squared Error (MSE)**:
  \[
  \text{MSE} = \frac{1}{n} \sum_{i=1}^n (y_i - \hat{y}_i)^2
  \]
- **Root Mean Squared Error (RMSE)**:
  \[
  \text{RMSE} = \sqrt{\text{MSE}}
  \]
- **Mean Absolute Percentage Error (MAPE)**:
  \[
  \text{MAPE} = \frac{1}{n} \sum_{i=1}^n \left| \frac{y_i - \hat{y}_i}{y_i} \right| \cdot 100
  \]
- **Mean Absolute Deviation (MAD)**:
  \[
  \text{MAD} = \frac{1}{n} \sum_{i=1}^n |y_i - \hat{y}_i|
  \]
- **Running Sum of Forecast Errors (RSFE)**:
  \[
  \text{RSFE} = \sum_{i=1}^n (y_i - \hat{y}_i)
  \]
- **Tracking Signal**:
  \[
  \text{Tracking Signal} = \frac{\text{RSFE}}{\text{MAD}}
  \]

#### Implementation
- **Backend** (`app.py`):
  - Uses `statsmodels.tsa.holtwinters.ExponentialSmoothing`.
  - Fits the model with user-specified parameters.
  - Splits data into training and test sets, forecasts test and future periods.
  - Computes metrics and generates Plotly JSON.

### Prophet

#### Overview
Prophet is a forecasting model by Facebook, designed for time-series data with trends, seasonality, and holidays. It handles missing data and outliers robustly.

#### Parameters
- **Growth**:
  - Linear: Linear trend.
  - Logistic: S-shaped trend with a carrying capacity.
- **Changepoint Prior Scale**: Flexibility for trend changes (default: 0.05).
- **Seasonality Mode**:
  - Additive: Constant seasonal effects.
  - Multiplicative: Seasonal effects scale with the trend.
- **Yearly/Weekly/Daily Seasonality**: Enable/disable seasonal components.
- **Interval Width**: Confidence interval width (default: 0.8).
- **Seasonality Prior Scale**: Flexibility for seasonal components (default: 12).
- **Number of Changepoints**: Number of potential trend change points (default: 10).

#### Formulas
Prophet models the time series as:
\[
y(t) = g(t) + s(t) + h(t) + \epsilon_t
\]
- \(g(t)\): Trend (linear or logistic).
- \(s(t)\): Seasonal components.
- \(h(t)\): Holiday effects (not used in this project).
- \(\epsilon_t\): Error term.

- **Trend** (\(g(t)\)):
  - Linear:
    \[
    g(t) = (k + \sum_i \delta_i \cdot a_i(t)) t + (m + \sum_i \gamma_i \cdot a_i(t))
    \]
    - \(k\): Base growth rate.
    - \(\delta_i\): Change in growth rate at changepoint \(i\).
    - \(a_i(t)\): Indicator for changepoint \(i\).
    - \(m\): Offset.
    - \(\gamma_i\): Offset adjustment.
  - Logistic:
    \[
    g(t) = \frac{C}{1 + e^{-(k + \sum_i \delta_i \cdot a_i(t))(t - (m + \sum_i \gamma_i \cdot a_i(t)))}}
    \]
    - \(C\): Carrying capacity (set automatically or user-defined).

- **Seasonality** (\(s(t)\)):
  \[
  s(t) = \sum_{n=1}^N \left( a_n \cos\left(\frac{2\pi n t}{P}\right) + b_n \sin\left(\frac{2\pi n t}{P}\right) \right)
  \]
  - \(P\): Period (e.g., 365.25 for yearly).
  - \(a_n, b_n\): Fourier coefficients.
  - Additive: Added to trend.
  - Multiplicative: Multiplied by trend.

- **Changepoints**:
  - Controlled by `changepoint_prior_scale` (\(\lambda\)):
    \[
    \delta_i \sim \text{Laplace}(0, \lambda)
    \]
  - Higher \(\lambda\) allows more abrupt changes.

- **Forecast**:
  \[
  \hat{y}(t) = g(t) + s(t) + \epsilon_t
  \]
  - Confidence intervals are computed using Monte Carlo sampling.

#### Metrics
- Same as Exponential Smoothing (MAE, MSE, RMSE, MAPE, MAD, RSFE, Tracking Signal).
- Additional Metrics:
  - **Historical Growth Rate (Alpha)**:
    \[
    \alpha = \left( \frac{y_{\text{last}}}{y_{\text{first}}} \right)^{\frac{1}{n}} - 1
    \]
    - \(n\): Number of periods.
  - **Historical Variance**:
    \[
    \text{Var} = \frac{1}{n-1} \sum_{i=1}^n (y_i - \bar{y})^2
    \]
  - **Historical Standard Deviation**:
    \[
    \text{SD} = \sqrt{\text{Var}}
    \]
  - **Forecast Confidence Variance**: Variance of forecast confidence intervals.

#### Implementation
- **Backend** (`app.py`):
  - Uses `prophet.Prophet`.
  - Configures model with user parameters (e.g., `growth`, `seasonality_mode`).
  - Fits model on training data, forecasts test and future periods.
  - Computes metrics and generates Plotly JSON with historical, test, and forecast traces.

### Risk Aversion Analysis

#### Overview
The risk aversion analysis (`riskaversion.html`) calculates the proportion of orders to allocate to two commodity sources based on forecasted prices, historical prices, and risk preferences (moderate or high risk aversion).

#### Inputs
- \(P_{1t}\): Forecasted price of commodity 1.
- \(P_{2t}\): Forecasted price of commodity 2.
- \(P_{10}\): Last known price of commodity 1.
- \(P_{20}\): Last known price of commodity 2.
- \(t\): Forecast time (months).
- \(\text{Var}(P_{1t})\): Variance of forecasted price 1.
- \(T\): Contract duration (months).

#### Calculations
1. **Growth Rates**:
   \[
   \alpha_1 = \left( \frac{P_{1t}}{P_{10}} \right)^{\frac{1}{t}} - 1
   \]
   \[
   \alpha_2 = \left( \frac{P_{2t}}{P_{20}} \right)^{\frac{1}{t}} - 1
   \]
   - \(\alpha_1, \alpha_2\): Growth rates for commodities 1 and 2.

2. **Price Ratio**:
   \[
   P_0 = \frac{P_{10}}{P_{20}}
   \]

3. **Growth Difference**:
   \[
   \alpha = \alpha_1 - \alpha_2
   \]

4. **Volatility (Beta)**:
   \[
   \beta_1 = \sqrt{\text{Var}(P_{1t})}
   \]

5. **Moderate Risk Aversion (MRA)**:
   - Intermediate terms:
     \[
     a = \frac{P_0}{\alpha} (e^{\alpha T} - 1)
     \]
     \[
     b = T - a
     \]
     \[
     c = 2\alpha + \beta_1^2
     \]
     \[
     d = \frac{P_0^2}{c} (e^{c T} - 1)
     \]
   - Proportion:
     \[
     \text{MRA} = \frac{a}{d}
     \]
   - Clipped to [0, 1] if outside bounds.

6. **High Risk Aversion (HRA)**:
   - Intermediate terms:
     \[
     Q = \frac{e^{3T(\alpha + \beta_1^2)} - 1}{3(\alpha + \beta_1^2)}
     \]
     \[
     R = \frac{e^{T(2\alpha + \beta_1^2)} - 1}{2\alpha + \beta_1^2}
     \]
     \[
     S = \frac{e^{\alpha T} - 1}{\alpha}
     \]
     \[
     X = -3T + 3Q P_0^3 - 9R P_0^2 + 9S P_0
     \]
     \[
     Y = 6T + 6R P_0^2 - 12S P_0
     \]
     \[
     Z = 3S P_0 - 3T
     \]
   - Proportion:
     \[
     \text{HRA} = \frac{X}{Y}
     \]
   - If \(\alpha = 0\):
     \[
     \text{HRA} = \frac{P_0}{Z}
     \]
   - Clipped to [0, 1] if outside bounds.

#### Implementation
- **Backend** (`app.py`):
  - Validates inputs (positive numbers).
  - Computes growth rates, price ratio, volatility.
  - Calculates MRA and HRA proportions, handling edge cases (e.g., \(\alpha = 0\), negative discriminant).
  - Returns JSON with results or errors.
- **Frontend** (`riskaversion.html`):
  - Displays intermediate terms and final proportions.
  - Shows clipping statements if proportions are adjusted.
  - Generates a PDF with results using `/download_RAC_pdf`.

## Graphs
Graphs are rendered using Plotly in `fred_commodity_history.html`, `fred_exponential_forecast.html`, and `fred_prophet_forecast.html`.

- **Historical Data** (`fred_commodity_history.html`):
  - Line plots of price vs. date for selected titles.
  - Data fetched from `/get_title_data`.
  - Example trace: `{ x: dates, y: values, type: 'scatter', mode: 'lines', name: title }`.

- **Exponential Smoothing** (`fred_exponential_forecast.html`):
  - Plots historical values, test period forecasts, and future forecasts.
  - Includes training data, test actual vs. predicted, and forecast horizon.
  - Example layout: `{ title: 'Exponential Smoothing Forecast', xaxis: { title: 'Date' }, yaxis: { title: 'Price' } }`.

- **Prophet** (`fred_prophet_forecast.html`):
  - Plots historical values, test period forecasts, future forecasts, and confidence intervals.
  - Includes upper/lower bounds for forecasts.
  - Example trace for forecast: `{ x: forecast_dates, y: forecast_values, type: 'scatter', mode: 'lines', name: 'Forecast' }`.
  - Confidence interval: `{ x: forecast_dates, y: forecast_upper, fill: 'tonexty', fillcolor: 'rgba(0,100,80,0.2)', name: 'Upper Bound' }`.

## File Names and Folder Structure

To run the application, organize the files as follows:

```
commodity_forecasting_project/
├── app.py
├── static/
│   └── fred_expo_styling.css
├── templates/
│   ├── fred_commodity_history.html
│   ├── fred_exponential_forecast.html
│   ├── fred_prophet_forecast.html
│   ├── home.html
│   └── riskaversion.html
├── requirements.txt
└── .env
```

### File Descriptions
- **app.py**: Flask application with backend logic.
- **static/fred_expo_styling.css**: CSS for frontend styling.
- **templates/**: HTML templates for each page.
- **requirements.txt**: Python dependencies (example below).
- **.env**: Environment variables (e.g., `FRED_API_KEY=your_api_key`).

### Example `requirements.txt`
```
flask==2.0.1
fredapi==0.5.0
pandas==1.4.3
numpy==1.22.4
statsmodels==0.13.2
prophet==1.1.1
plotly==5.9.0
openpyxl==3.0.10
python-dotenv==0.20.0
reportlab==3.6.11
```

### Setup Instructions
1. **Install Dependencies**:
   - Create a virtual environment: `python -m venv venv`
   - Activate: `venv\Scripts\activate` (Windows) or `source venv/bin/activate` (Linux/macOS)
   - Install: `pip install -r requirements.txt`

2. **Configure Environment**:
   - Create `.env` with: `FRED_API_KEY=your_api_key_here`
   - Obtain a FRED API key from [fred.stlouisfed.org](https://fred.stlouisfed.org/).

3. **Run the Application**:
   - Ensure the folder structure is correct.
   - Run: `python app.py`
   - Access: `http://localhost:5000`

4. **External Libraries**:
   - Uses CDN-hosted Plotly, jsPDF, html2canvas, jQuery, MathJax. Host locally in `static/` for offline use if needed.

5. **Troubleshooting**:
   - Verify FRED API key if data fetching fails.
   - Ensure sufficient data for selected titles and frequency.
   - Check inputs for risk aversion (positive numbers, valid variance).

## Additional Insights
- **Performance**:
  - Large datasets or multiple titles may slow forecasting. Cache FRED data or limit titles for optimization.
  - Prophet can be computationally intensive; consider reducing `n_changepoints` for faster processing.
- **Security**:
  - Sanitize inputs in `app.py` to prevent injection attacks.
  - Secure the FRED API key in `.env`.
- **Extensibility**:
  - Add new models by creating endpoints and templates (e.g., ARIMA).
  - Enhance risk aversion with additional models or parameters.
- **Graphs**:
  - Plotly graphs are responsive and interactive (zoom, pan, hover).
  - Customize layouts in `app.py` (e.g., colors, titles) for branding.

This report provides a comprehensive guide to your commodity forecasting application, covering architecture, user flow, calculations, and setup. Let me know if you need further details, code modifications, or a different format (e.g., PDF).
