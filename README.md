# Final13.0LMS
package main

import (
    "encoding/json"
    "net/http"
    "regexp"
    "strconv"
)

// RequestBody описывает тело запроса
type RequestBody struct {
    Expression string `json:"expression"`
}

// ResponseBody описывает структуру ответа
type ResponseBody struct {
    Result string `json:"result,omitempty"`
    Error  string `json:"error,omitempty"`
}

// Проверка на валидность выражений
var validExpression = regexp.MustCompile(`^[0-9+\-*/() ]+$`)

// CalculateHandler обрабатывает POST-запросы
func CalculateHandler(w http.ResponseWriter, r *http.Request) {
    var reqBody RequestBody
    err := json.NewDecoder(r.Body).Decode(&reqBody)
    if err != nil || !validExpression.MatchString(reqBody.Expression) {
        respondWithError(w, "Invalid expression", http.StatusBadRequest)
        return
    }

    result, evalErr := evaluateExpression(reqBody.Expression)
    if evalErr != nil {
        respondWithError(w, "Error evaluating expression", http.StatusInternalServerError)
        return
    }

    respondWithJSON(w, ResponseBody{Result: result}, http.StatusOK)
}

// evaluateExpression вычисляет значение выражения
func evaluateExpression(expr string) (string, error) {
    // Здесь должен быть ваш алгоритм для вычисления выражений
    // Замените этот код на использование библиотек или вашего парсера
    return strconv.Itoa(42), nil // временное значение
}

// respondWithError отправляет ответ об ошибке
func respondWithError(w http.ResponseWriter, message string, code int) {
    respondWithJSON(w, ResponseBody{Error: message}, code)
}

// respondWithJSON отправляет JSON ответ
func respondWithJSON(w http.ResponseWriter, data ResponseBody, code int) {
    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(code)
    json.NewEncoder(w).Encode(data)
}

func main() {
    http.HandleFunc("/api/v1/calculate", CalculateHandler)
    http.ListenAndServe(":8080", nil)
}
