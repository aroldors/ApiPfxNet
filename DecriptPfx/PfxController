using System;
using System.IO;
using System.Linq;
using System.Security.Cryptography.X509Certificates;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;

namespace PfxReaderApi.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class PfxController : ControllerBase
    {
        [HttpPost("read")]
        public IActionResult ReadPfx(IFormFile file, string password)
        {
            try
            {               
                
                if (file == null || string.IsNullOrEmpty(password))
                {
                    return BadRequest("Arquivo PFX e senha são necessários.");
                }

                using var memoryStream = new MemoryStream();
                file.CopyTo(memoryStream);
                var pfxBytes = memoryStream.ToArray();

                var cert = new X509Certificate2(pfxBytes, password, X509KeyStorageFlags.Exportable);

                var validFrom = cert.NotBefore;
                var validTo = cert.NotAfter;

                var subject = cert.Subject;
                var cnpj = ExtractCnpj(subject);
                var companyName = ExtractCompanyName(subject);

                var result = new
                {
                    ValidFrom = validFrom,
                    ValidTo = validTo,
                    CNPJ = cnpj,
                    CompanyName = companyName
                };

                return Ok(result);
            }
            catch (Exception ex)
            {
                return StatusCode(500, $"Erro ao processar o arquivo PFX: {ex.Message}");
            }
        }

        private string? ExtractCnpj(string subject)
        {
            var cnpjPrefix = ":";
            var cnpjStartIndex = subject.IndexOf(cnpjPrefix);
            if (cnpjStartIndex >= 0)
            {
                cnpjStartIndex += cnpjPrefix.Length;
                var cnpjEndIndex = subject.IndexOf(',', cnpjStartIndex);
                if (cnpjEndIndex == -1)
                {
                    cnpjEndIndex = subject.Length;
                }

                var cnpj = subject.Substring(cnpjStartIndex, cnpjEndIndex - cnpjStartIndex);
                if (cnpj.All(char.IsDigit) && cnpj.Length == 14)
                {
                    return cnpj;
                }
            }
            return null;
        }

        private string? ExtractCompanyName(string subject)
        {
            var companyNamePrefix = "CN=";
            var companyNameStartIndex = subject.IndexOf(companyNamePrefix);
            if (companyNameStartIndex >= 0)
            {
                companyNameStartIndex += companyNamePrefix.Length;
                var companyNameEndIndex = subject.IndexOf(':', companyNameStartIndex);
                if (companyNameEndIndex == -1)
                {
                    companyNameEndIndex = subject.Length;
                }

                return subject.Substring(companyNameStartIndex, companyNameEndIndex - companyNameStartIndex);
            }
            return null;
        }
    }
}
