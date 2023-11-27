#include <iostream>
#include <vector>
#include <string>
#include <cstdlib>

class Flight {
public:
    Flight(const std::string& destination) : destination(destination), fuel(100) {
        // Генеруємо випадкове значення для номера аеропорту призначення
        destination_airport = rand() % num_airports;
    }

    void refuel() {
        fuel = 100;
    }

    std::string destination;
    int destination_airport; // Номер аеропорту призначення
    int fuel;
};

class Airport {
public:
    Airport(const std::string& name) : name(name), active(true) {}

    void close() {
        active = false;
    }

    void reopen() {
        active = true;
    }

    void addFlight(const Flight& flight) {
        flights.push_back(flight);
    }

    void redirectFlights(Airport& closedAirport) {
        for (Flight& flight : flights) {
            if (flight.destination_airport == closedAirport.index) {
                // Перенаправити рейс до найближчого активного аеропорту
                int nearestAirport = findNearestAirport();
                flight.destination_airport = nearestAirport;
                airports[nearestAirport].addFlight(flight);
            }
        }
    }

    int findNearestAirport() {
        int nearestAirport = -1;
        int minDistance = INT_MAX;

        for (int i = 0; i < num_airports; i++) {
            if (i != index && airports[i].active) {
                // Обчислюємо відстань (замість цього можна використовувати реальний розрахунок відстані)
                int distance = abs(index - i);
                if (distance < minDistance) {
                    nearestAirport = i;
                    minDistance = distance;
                }
            }
        }

        return nearestAirport;
    }

    std::string name;
    bool active;
    std::vector<Flight> flights;
};

const int num_airports = 5;
std::vector<Airport> airports(num_airports);

int main() {
    // Ініціалізуємо аеропорти та присвоюємо їм індекси
    for (int i = 0; i < num_airports; i++) {
        airports[i] = Airport("Airport" + std::to_string(i));
        airports[i].index = i;
    }

    // Додамо рейси до аеропортів
    for (int i = 0; i < num_airports; i++) {
        for (int j = 0; j < 5; j++) {
            Flight flight("Airport" + std::to_string(rand() % num_airports));
            airports[i].addFlight(flight);
        }
    }

    // Закриваємо перший аеропорт
    airports[0].close();

    // Перенаправляємо рейси в закритому аеропорту
    for (int i = 1; i < num_airports; i++) {
        airports[i].redirectFlights(airports[0]);
    }

    // Виводимо стан аеропортів
    for (int i = 0; i < num_airports; i++) {
        std::cout << airports[i].name << " is " << (airports[i].active ? "open" : "closed") << std::endl;
    }

    return 0;
}
