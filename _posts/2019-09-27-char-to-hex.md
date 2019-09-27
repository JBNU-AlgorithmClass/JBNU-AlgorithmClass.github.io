# To change char input data to hex output (Arduino)



- - -

졸업 프로젝트에서 1:n 모듈을 추가하기 위해 생각했던 것이다.

어플을 만들 시간도 없었기 때문에 블루투스 통신으로 데이터를 주고받을 수 있는 어플을 하나 다운 받았고, 데이터를 확인해본 결과 char 데이터만 보낼 수 있었다.

그 데이터를 받아서 16진수로 주소를 할당해야했기 때문에 받은 데이터를 변환하는게 필요했다.

예를들어 사용자가 29 a8 47 b3 c9 이런식으로 보낸다면 이것을 0x29 0xa8 0x47 0xb3 0xc9 이런식으로 바꿔서 주소를 할당하는 것이다.

만약 입력이 잘못되면 에러 메세지를 띄우고 다시 입력하도록 한다. 데이터의 마지막은 문자 'p'로 구분하게 했다.

코드는 다음과 같다.

- - -



I made this code to add 1:n modules when I doing graduation project.

I downloaded an app at play store which can communicate with bluetooth because I had not enough time to make an app. after checking the communication, I noticed the app could send only char data.

I need to change the data since I had to allocate hexadecimal address.

For example, if user send the text like 29 a8 47 b3 c9, allocate the address as 0x29 0xa8 0x47 0xb3 0xc9

If there was mistyping, error message is going to appear. And, the user has to type the text again. Also, end of the message was char 'p'

Here is the code.


- - -


    char input[15] = {'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z'};
    byte addr[5];
    
    void setup() {
      Serial.begin(9600);
    }

    void loop() {
      Serial.println("start");
      recevingData(input);
      partChangeData(input);
  
      for(int i=0; i<5; i++){
        Serial.print(addr[i], HEX);
        Serial.print(" ");
      }
      Serial.println("");
      Serial.println("end");
      delay(500);
    }


    void recevingData(char input[]){
        int rec_cnt = 0;
      bool data_flag = false;
      bool flag = true;

      Serial.println("enter the message");
      
      while(flag){    
        if(Serial.available()){
          if(input[rec_cnt] == 'z'){
            input[rec_cnt] = Serial.read();
            rec_cnt++;
            data_flag = true;
          }      
        }
        
        if(rec_cnt == strlen(input)){  // if end message were error,     receive message again
          if(input[strlen(input)-1] == 'p')  flag = false;
          else {
            rec_cnt = 0;
            for(int i=0; i<strlen(input); i++){
              input[i] = 'z';
            }
            Serial.println("end message error");
          }
        }
        
        if(rec_cnt <= strlen(input)-1){
          if(input[rec_cnt-1] == 'p'){
            rec_cnt = 0;
            for(int i=0; i<strlen(input); i++){
              input[i] = 'z';
            }
            Serial.println("message error");
          }
        }
    
        if((rec_cnt == 3) || (rec_cnt == 6) || (rec_cnt == 9) || (rec_cnt == 12)){
          if(input[rec_cnt-1] != ' '){
            rec_cnt = 0;
            for(int i=0; i<strlen(input); i++){
              input[i] = 'z';
            }
            Serial.println("message error");
          }
        }
      }
    }

    void partChangeData(char input[]){
      char tmp[2] = {'z', 'z'};
      int tmp_cnt = 0;
      for(int i=0; i<5; i++){
        for(int j=0; j<3; j++){
          if((tmp_cnt % 3) != 2){
            tmp[j] = input[tmp_cnt];
          }
          else{
            addr[i] = changeData(tmp);
          }
          tmp_cnt ++;
        }
      }
    }

    byte changeData(char input[]){
      int new_num[2] = {0, 0};
      byte change_data = 0x00;
    
      bool change_error_flag = false;
      bool data_flag = true;
    
      if(data_flag){
        for(int i=0; i<2; i++){
          if(int(input[i]) >= 48 && int(input[i]) <= 57){
            new_num[i] = int(input[i]) - 48;
          }
          else if(int(input[i]) >= 65 && int(input[i]) <= 70){
            new_num[i] = int(input[i]) - 55;
          }
          else if(int(input[i]) >= 97 && int(input[i]) <= 102){
            new_num[i] = int(input[i]) - 87;
          }
          else{
            Serial.println("invalid input. type again.");
            new_num[0] = 'z';
            new_num[1] = 'z';
            input[0] = 'z';
            input[1] = 'z';
            i = 0;
            change_error_flag = true;
            break;
          }
        }
      
        int cnt = 0;
        if(!change_error_flag){
          for(int i=0; i<16; i++){
            for(int j=0; j<16; j++){
              if((new_num[0] == i) && (new_num[1] == j)){
                change_data = cnt;
              }
              cnt++;
            }
          }
        }
      }
      data_flag = false;
      input[0] = 'z';
      input[1] = 'z';
      return change_data;
    }