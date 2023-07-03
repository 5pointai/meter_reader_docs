# Meter Reading OCR API Documentation

## Overview

The Meter Reading OCR API allows you to extract meter readings and serial numbers from images of meter displays. The API accepts image uploaded as a multi-part file and returns the bounding box values, OCR of the meter display screen, and serial number.


## Base URL

https://f076dkkw02.execute-api.eu-north-1.amazonaws.com/dev

## Authorization

To use this API, you need an API key. Please contact the API administrator to obtain an API key. Once you have the API key, include it in your requests by setting the `x-api-key` header.

**Note:** It's essential to keep your API key secret. Do not share your API key in public repositories or expose it in client-side code. Store the key securely and use server-to-server communication to interact with the API.

## Endpoint

### POST /meter_reading

Extracts meter readings and serial numbers from the meter display image.

#### Input Payload

The request should use the `multipart/form-data` format with the following field:

`meter_image`: The meter image file to be uploaded.


```json
{
  "image": "Browse the meter image file"
}
```


## Headers

`Content-Type: multipart/form-data`

`x-api-key: YOUR_API_KEY`


## Output
The API returns a JSON object with information about uploaded image.

```json
{
  "statusCode": 200,
  "body": "{\"meter_reader_data\": {\"screen\": [457, 151, 744, 206, \"0020750.4\", 0.8487719893455505], \"serial_no\": [347, 374, 561, 429, \"20539675\", 0.9091470241546631]}}",
  "message": "\"Lambda trigger successfully..!\""
}
```

## Output Description

`statusCode`: The HTTP status code representing the result of the operation.

`body`: A string containing a JSON object with the extracted information.

`meter_reader_data`: The extracted data from the meter display image.

`screen`: The bounding box coordinates (x1, y1, x2, y2), meter reading, and confidence score for the meter reading.

`serial_no`: The bounding box coordinates (x1, y1, x2, y2), serial number, and confidence score for the serial number.

`message`: A string describing the result of the operation.


## Usage Example

### Python Example

```python
import requests
import json

url = "https://f076dkkw02.execute-api.eu-north-1.amazonaws.com/dev/meter_reading"
api_key = "YOUR_API_KEY"
headers = {
    "Content-Type": "multipart/form-data",
    "x-api-key": api_key
}
image_path = "/folder_path/2.jpg"
with open(image_path, "rb") as image_file:
    files = {"image": image_file}
    response = requests.post(url, headers=headers, files=files)

response_data = json.loads(response.text)

print(response_data)
```

## Android Example (Kotlin)
### Add the following dependencies to your build.gradle file:

```android
implementation 'com.squareup.retrofit2:retrofit:2.9.0'
implementation 'com.squareup.retrofit2:converter-gson:2.9.0'
implementation 'com.squareup.okhttp3:logging-interceptor:4.9.1'
```

### Create an interface for the API service:

```android
import retrofit2.Call
import retrofit2.http.Body
import retrofit2.http.Headers
import retrofit2.http.POST

interface MeterReadingOCRService {
    @Headers("Content-Type: application/json")
    @POST("meter_reading")
    fun getMeterReading(
        @Body payload: Map<String, String>,
        @retrofit2.http.Header("x-api-key") apiKey: String
    ): Call<MeterReadingResponse>
}
```

### Create a data class for the API response:

```android
import com.google.gson.annotations.SerializedName

data class MeterReadingResponse(
    @SerializedName("statusCode")
    val statusCode: Int,

    @SerializedName("body")
    val body: String,

    @SerializedName("message")
    val message: String
)
```

### Use the Retrofit library to make the API call:

```android
import okhttp3.OkHttpClient
import okhttp3.logging.HttpLoggingInterceptor
import retrofit2.Retrofit
import retrofit2.converter.gson.GsonConverterFactory

val interceptor = HttpLoggingInterceptor().apply {
    level = HttpLoggingInterceptor.Level.BODY
}

val client = OkHttpClient.Builder().apply {
    addInterceptor(interceptor)
}.build()

val retrofit = Retrofit.Builder()
    .baseUrl("https://f076dkkw02.execute-api.eu-north-1.amazonaws.com/dev/")
    .addConverterFactory(GsonConverterFactory.create())
    .client(client)
    .build()

val service = retrofit.create(MeterReadingOCRService::class.java)
val payload = mapOf("image_data" to "Image base64 data")
val apiKey = "YOUR_API_KEY"

val call = service.getMeterReading(payload, apiKey)
call.enqueue(object : Callback<MeterReadingResponse> {
    override fun onResponse(
        call: Call<MeterReadingResponse>,
        response: Response<MeterReadingResponse>
    ) {
        response.body()?.let { meterReadingResponse ->
            // Handle the response
            Log.d("MeterReadingResponse", meterReadingResponse.toString())
        }
    }

    override fun onFailure(call: Call<MeterReadingResponse>, t: Throwable) {
        // Handle failure
        Log.e("MeterReadingError", t.message, t)
    }
})
```

