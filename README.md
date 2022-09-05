# Test ReCAPTCHA

Testing and POC for Google ReCAPTCHA V3 for Backend.

> This is for run on OSX only.

## prerequisite

Please install these prerequisite to run and test on your local:

1. Ruby, install through [rbenv](https://www.digitalocean.com/community/tutorials/how-to-install-ruby-on-rails-with-rbenv-on-macos) first, after that install Ruby `3.1.2` via rbenv.
2. Install Firefox web-driver `geckodriver` if you want to run the [scrapper](/scrapper) script on your local. run: `brew install geckodriver`
3. On this project root directory, run: `bundle install`

## How to run

1. Run the backend service

  ```bash
  ./main
  ```

  Wait for the server to start properly.

2. Go to [http://localhost:4567](http://localhost:4567) for access to testing page.

3. Hit the `Post data to Backend` blue button, and wait for the response result.

4. Alternatively, run the scrapper script.

  ```bash
  ./scrapper
  ```

## Documentation

### Reference

- [Google ReCAPTCHA doc](https://developers.google.com/recaptcha/intro)
- [Google ReCAPTCHA V3 doc](https://developers.google.com/recaptcha/docs/v3)
- [Verify input response token on server side doc](https://developers.google.com/recaptcha/docs/verify)

## Testing

1. The webpage implementation for this is using ReCAPTCHA V3 approach [Automatically bind the challenge to a button](https://developers.google.com/recaptcha/docs/v3#automatically_bind_the_challenge_to_a_button).

2. When submit the HTML form, ReCAPTCHA JS API, will automatically put field `g-recaptcha-response` for input response token, on payload of the request. __NOTE:__ later when implementation FE/Apps should get this token value, and send it as a field `captchaToken` when TTD POST ORDER request (as on contract).

    Input response token value sample:

    ```
    03ANYolqvUUdPy2egLgE8ZuOVABZnxTF8X26Rf4yWkP82YIQysVHjxGXctFKYV71AWakW9bIMB1kiDW_Xxk1Y_lZr8AmJP6cmvrEvt7assXXnUahuDx0GRb-H0dWZPiwciTezuEANBBf0XjQABgyfT59bAdCL9rwcp7WyXS63QYYTCoJmC5pPpiHQ8uEhdseCNHtOGWJOC2sy6PcrQsH_ZAtkQB1gim6Tx-1aqFwm9oPGpvwothRTsMftw6E_C80oxqhpAdPU7j0azheVzsqXYGfq8eRfKeseYqu4Uc4oJqFrHr_TGyZ2jXWZjCrEMJAw1r63BiStn-t5Ptn9KFl892DdcFAveObM1agYX2MSLSghlmrudFuzpzHH9QYdaGQr2M_7x6_67lei31ZEhr1OOtcYRtEHJZXPHJL4cd7to48LV3UHa5bDyzdBVD2E0-7QSHp27qFlAYqys3Q_f9DT-vl4T8pvU_nmZ4WmE_k3ZsCMQO_LHDUjCk8suXKGcdhtvUgQrqt4_yXtW
    ```

3. Backend will verify this input response token by do a HTTP request __POST__ to `https://www.google.com/recaptcha/api/siteverify` ([Doc](https://developers.google.com/recaptcha/docs/verify)).

4. There are several possibilities __response__ from Google ReCAPTCHA:

    - __When the input response token is valid__
      
      Response HTTP Code: 200

      ```json
      {
        "success": true,
        "challenge_ts": "2022-09-04T23:17:51Z",
        "hostname": "localhost",
        "score": 0.9,
        "action": "submit"
      }
      ```

    - __When invalid input response token__
      
      Response HTTP Code: 200

      ```json
      {
        "success": false,
        "error-codes": [
          "invalid-input-response"
        ]
      }
      ```

    - __When invalid secret key__

      Response HTTP Code: 200

      ```json
      {
        "success": false,
        "error-codes": [
          "invalid-input-secret"
        ]
      }
      ```
    
    - __When input response token has expired__

      Response HTTP Code: 200

      ```json
      {
        "success": false,
        "error-codes": [
          "timeout-or-duplicate"
        ]
      }
      ```

5. __In summary:__ Backend should checking for only HTTP Code 200 and payload field `success` should `true` for valid CAPTCHA request. 

## Screenshots

- Testing web page

  ![Imgur](/screenshots/testing-web-page.png)

- Backend incoming request console out

  ![Imgur](/screenshots/incoming-request-BE-log.png)

- Running scrapper console out

  ![Imgur](/screenshots/running-scrapper-console-out.png)
