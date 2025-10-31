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

int main() {
    setlocale(LC_ALL, "rus");
    const string request_file = "C:/CARS/REQUEST.txt";

    while (true) {
        cout << "Введите модель автомобиля (или 'exit' для выхода): ";
        cin >> C.model;

        if (C.model == "exit" || C.model == "esc") {
            cout << "Завершение работы клиента." << endl;
            break;
        }

        cout << "Введите возраст автомобиля (лет): ";
        cin >> C.age;
        cout << "Введите пробег (тыс. км): ";
        cin >> C.mileage;
        cout << "Введите состояние (1-10): ";
        cin >> C.condition;
        cout << "Введите топливную эффективность (1-10): ";
        cin >> C.fuel_efficiency;

        // Создание файла с данными об автомобиле
        string data_file = "C:/CARS/" + C.model + ".txt";
        ofstream fData(data_file);
        fData << C.age << endl
            << C.mileage << endl
            << C.condition << endl
            << C.fuel_efficiency;
        fData.close();

        // Добавление запроса в файл запросов
        ofstream fRequest(request_file, ios::app);
        fRequest << C.model << endl;
        fRequest.close();

        Sleep(2000); // Ожидание обработки сервером

        // Чтение ответа от сервера
        ifstream fAnswer(data_file);
        string line, last_line;
        while (getline(fAnswer, line)) {
            if (!line.empty()) last_line = line;
        }
        fAnswer.close();

        // Вывод результата
        cout << "Рекомендация для " << C.model << ": ";
        if (last_line == "ECONOMY") cout << "Эконом-класс" << endl;
        else if (last_line == "COMFORT") cout << "Комфорт-класс" << endl;
        else if (last_line == "PREMIUM") cout << "Премиум-класс" << endl;
        else if (last_line == "REJECT") cout << "Не рекомендуется для аренды" << endl;
        cout << "-----------------------" << endl;
    }
    return 0;
}