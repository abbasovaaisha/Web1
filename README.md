#include <iostream>
#include <fstream>
#include <string>
#include <windows.h>
using namespace std;

struct Car {
    string model;
    int age;
    int mileage;
    int condition;
    int fuel_efficiency;
} C;

long current_request_size = 0;

int main() {
    setlocale(LC_ALL, "rus");
    const string request_file = "C:/CARS/REQUEST.txt";
    cout << "Сервер запущен и работает..." << endl;

    // Инициализация текущего размера файла запросов
    ifstream fRequest(request_file);
    fRequest.seekg(0, ios::end);
    current_request_size = fRequest.tellg();
    fRequest.close();

    while (true) {
        ifstream fRequest(request_file);
        fRequest.seekg(0, ios::end);

        if (fRequest.tellg() > current_request_size) {
            fRequest.seekg(current_request_size, ios::beg);
            getline(fRequest, C.model);
            current_request_size = fRequest.tellg();
            fRequest.close();

            string data_file = "C:/CARS/" + C.model + ".txt";
            ifstream fData(data_file);
            if (fData.is_open()) {
                fData >> C.age;
                fData >> C.mileage;
                fData >> C.condition;
                fData >> C.fuel_efficiency;
                fData.close();

                string recommendation;
                int score = C.condition * 2 + C.fuel_efficiency * 2 - C.age - C.mileage / 50;

                if (C.condition < 3 || C.age > 15) {
                    recommendation = "REJECT";
                }
                else if (score >= 25) {
                    recommendation = "PREMIUM";
                }
                else if (score >= 15) {
                    recommendation = "COMFORT";
                }
                else {
                    recommendation = "ECONOMY";
                }

                ofstream fAnswer(data_file, ios::app);
                fAnswer << endl << recommendation;
                fAnswer.close();

                cout << "Обработан автомобиль: " << C.model
                    << " Рекомендация: " << recommendation << endl;
            }
        }
        else {
            fRequest.close();
            Sleep(1000);
        }
    }
    return 0;
}