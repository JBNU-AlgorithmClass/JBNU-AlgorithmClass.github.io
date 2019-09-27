# This is test page.

이 코드는 졸업 프로젝트 당시 사용했던 코드 입니다

this code used for graduation project


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